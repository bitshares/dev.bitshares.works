
.. _lib-db:

*******************************************
db folder - object/index
*******************************************

This section shows each (db object/index information) process file in the db folder.  

File directory: (../ibraries/db/include/graphene/db/xxxxx.hhp)


.. contents:: Table of Contents
   :local:
   
-------

generic_index.hpp 
===============================


-------------------------

index.hpp
===============================

.. code-block:: cpp 

   class object_database;
   using fc::path;

index_observer
----------------------------

* used to get callbacks when objects change

.. code-block:: cpp 

   class index_observer
   {
      public:
         virtual ~index_observer(){}
         /** called just after the object is added */
         virtual void on_add( const object& obj ){}
         /** called just before obj is removed */
         virtual void on_remove( const object& obj ){}
         /** called just after obj is modified with new value*/
         virtual void on_modify( const object& obj ){}
   };

   
index
----------------------------

* abstract base class for accessing objects indexed in various ways.
* All indexes assume that there exists an object ID space that will grow forever in a seqential manner.  These IDs are used to identify the index, type, and instance of the object.
* Items in an index can only be modified via a call to modify and all references to objects outside of that callback are const references. 
* Most implementations will probably be some form of boost::multi_index_container which means that they can covnert a reference to an object to an iterator.  When at all possible save a pointer/reference to your objects rather than constantly looking them up by ID.

.. code-block:: cpp 

   class index
   {
      public:
         virtual ~index(){}

         virtual uint8_t object_space_id()const = 0;
         virtual uint8_t object_type_id()const = 0;

         virtual object_id_type get_next_id()const = 0;
         virtual void           use_next_id() = 0;
         virtual void           set_next_id( object_id_type id ) = 0;

         virtual const object&  load( const std::vector<char>& data ) = 0;
         /**
          *  Polymorphically insert by moving an object into the index.
          *  this should throw if the object is already in the database.
          */
         virtual const object& insert( object&& obj ) = 0;

         /**
          * Builds a new object and assigns it the next available ID and then
          * initializes it with constructor and lastly inserts it into the index.
          */
         virtual const object&  create( const std::function<void(object&)>& constructor ) = 0;

         /**
          *  Opens the index loading objects from a file
          */
         virtual void open( const fc::path& db ) = 0;
         virtual void save( const fc::path& db ) = 0;



         /** @return the object with id or nullptr if not found */
         virtual const object*      find( object_id_type id )const = 0;

         /**
          * This version will automatically check for nullptr and throw an exception if the
          * object ID could not be found.
          */
         const object&              get( object_id_type id )const
         {
            auto maybe_found = find( id );
            FC_ASSERT( maybe_found != nullptr, "Unable to find Object ${id}", ("id",id) );
            return *maybe_found;
         }

         virtual void               modify( const object& obj, const std::function<void(object&)>& ) = 0;
         virtual void               remove( const object& obj ) = 0;

         /**
          *   When forming your lambda to modify obj, it is natural to have Object& be the signature, but
          *   that is not compatible with the type erasue required by the virtual method.  This method
          *   provides a helper to wrap the lambda in a form compatible with the virtual modify call.
          *   @note Lambda should have the signature:  void(Object&)
          */
         template<typename Object, typename Lambda>
         void modify( const Object& obj, const Lambda& l ) {
            modify( static_cast<const object&>(obj), std::function<void(object&)>( [&]( object& o ){ l( static_cast<Object&>(o) ); } ) );
         }

         virtual void               inspect_all_objects(std::function<void(const object&)> inspector)const = 0;
         virtual fc::uint128        hash()const = 0;
         virtual void               add_observer( const shared_ptr<index_observer>& ) = 0;

         virtual void               object_from_variant( const fc::variant& var, object& obj, uint32_t max_depth )const = 0;
         virtual void               object_default( object& obj )const = 0;
   };
   

secondary_index
------------------------------------   
   
.. code-block:: cpp 

   class secondary_index
   {
      public:
         virtual ~secondary_index(){};
         virtual void object_inserted( const object& obj ){};
         virtual void object_removed( const object& obj ){};
         virtual void about_to_modify( const object& before ){};
         virtual void object_modified( const object& after  ){};
   };
  
  
base_primary_index
----------------------------------------------
  
* Defines the common implementation

.. code-block:: cpp 

   class base_primary_index
   {
      public:
         base_primary_index( object_database& db ):_db(db){}

         /** called just before obj is modified */
         void save_undo( const object& obj );

         /** called just after the object is added */
         void on_add( const object& obj );

         /** called just before obj is removed */
         void on_remove( const object& obj );

         /** called just after obj is modified */
         void on_modify( const object& obj );

         template<typename T, typename... Args>
         T* add_secondary_index(Args... args)
         {
            _sindex.emplace_back( new T(args...) );
            return static_cast<T*>(_sindex.back().get());
         }

         template<typename T>
         const T& get_secondary_index()const
         {
            for( const auto& item : _sindex )
            {
               const T* result = dynamic_cast<const T*>(item.get());
               if( result != nullptr ) return *result;
            }
            FC_THROW_EXCEPTION( fc::assert_exception, "invalid index type" );
         }

      protected:
         vector< shared_ptr<index_observer> >   _observers;
         vector< unique_ptr<secondary_index> >  _sindex;

      private:
         object_database& _db;
   };

primary_index
--------------------------------------

*  Wraps a derived index to intercept calls to create, modify, and remove so that callbacks may be fired and undo state saved. 
* @see http://en.wikipedia.org/wiki/Curiously_recurring_template_pattern
 
.. code-block:: cpp 
 
   template<typename DerivedIndex>
   class primary_index  : public DerivedIndex, public base_primary_index
   {
      public:
         typedef typename DerivedIndex::object_type object_type;

         primary_index( object_database& db )
         :base_primary_index(db),_next_id(object_type::space_id,object_type::type_id,0) {}

         virtual uint8_t object_space_id()const override
         { return object_type::space_id; }

         virtual uint8_t object_type_id()const override
         { return object_type::type_id; }

         virtual object_id_type get_next_id()const override              { return _next_id;    }
         virtual void           use_next_id()override                    { ++_next_id.number;  }
         virtual void           set_next_id( object_id_type id )override { _next_id = id;      }

         fc::sha256 get_object_version()const
         {
            std::string desc = "1.0";//get_type_description<object_type>();
            return fc::sha256::hash(desc);
         }

         virtual void open( const path& db )override
         { 
            if( !fc::exists( db ) ) return;
            fc::file_mapping fm( db.generic_string().c_str(), fc::read_only );
            fc::mapped_region mr( fm, fc::read_only, 0, fc::file_size(db) );
            fc::datastream<const char*> ds( (const char*)mr.get_address(), mr.get_size() );
            fc::sha256 open_ver;

            fc::raw::unpack(ds, _next_id);
            fc::raw::unpack(ds, open_ver);
            FC_ASSERT( open_ver == get_object_version(), "Incompatible Version, the serialization of objects in this index has changed" );
            try {
               vector<char> tmp;
               while( true ) 
               {
                  fc::raw::unpack( ds, tmp );
                  load( tmp );
               }
            } catch ( const fc::exception&  ){}
         }

         virtual void save( const path& db ) override 
         {
            std::ofstream out( db.generic_string(), 
                               std::ofstream::binary | std::ofstream::out | std::ofstream::trunc );
            FC_ASSERT( out );
            auto ver  = get_object_version();
            fc::raw::pack( out, _next_id );
            fc::raw::pack( out, ver );
            this->inspect_all_objects( [&]( const object& o ) {
                auto vec = fc::raw::pack( static_cast<const object_type&>(o) );
                auto packed_vec = fc::raw::pack( vec );
                out.write( packed_vec.data(), packed_vec.size() );
            });
         }

         virtual const object&  load( const std::vector<char>& data )override
         {
            const auto& result = DerivedIndex::insert( fc::raw::unpack<object_type>( data ) );
            for( const auto& item : _sindex )
               item->object_inserted( result );
            return result;
         }


         virtual const object&  create(const std::function<void(object&)>& constructor )override
         {
            const auto& result = DerivedIndex::create( constructor );
            for( const auto& item : _sindex )
               item->object_inserted( result );
            on_add( result );
            return result;
         }

         virtual const object& insert( object&& obj ) override
         {
            const auto& result = DerivedIndex::insert( std::move( obj ) );
            for( const auto& item : _sindex )
               item->object_inserted( result );
            on_add( result );
            return result;
         }

         virtual void  remove( const object& obj ) override
         {
            for( const auto& item : _sindex )
               item->object_removed( obj );
            on_remove(obj);
            DerivedIndex::remove(obj);
         }

         virtual void modify( const object& obj, const std::function<void(object&)>& m )override
         {
            save_undo( obj );
            for( const auto& item : _sindex )
               item->about_to_modify( obj );
            DerivedIndex::modify( obj, m );
            for( const auto& item : _sindex )
               item->object_modified( obj );
            on_modify( obj );
         }

         virtual void add_observer( const shared_ptr<index_observer>& o ) override
         {
            _observers.emplace_back( o );
         }

         virtual void object_from_variant( const fc::variant& var, object& obj, uint32_t max_depth )const override
         {
            object_id_type id = obj.id;
            object_type* result = dynamic_cast<object_type*>( &obj );
            FC_ASSERT( result != nullptr );
            fc::from_variant( var, *result, max_depth );
            obj.id = id;
         }

         virtual void object_default( object& obj )const override
         {
            object_id_type id = obj.id;
            object_type* result = dynamic_cast<object_type*>( &obj );
            FC_ASSERT( result != nullptr );
            (*result) = object_type();
            obj.id = id;
         }

      private:
         object_id_type _next_id;
   };

   
-------------------------
 
object.hpp
===============================

object
-----------------

* base for all database objects
* The object is the fundamental building block of the database and is the level upon which undo/redo operations are performed.  Objects are used to track data and their relationships and provide an efficient means to find and update information.
* Objects are assigned a unique and sequential object ID by the database within the id_space defined in the object.
* All objects must be serializable via FC_REFLECT() and their content must be faithfully restored.   Additionally all objects must be copy-constructable and assignable in a relatively efficient manner.  In general this means that objects should only refer to other objects by ID and avoid expensive operations when they are copied, especially if they are modified frequently.
* Additionally all objects may be annotated by plugins which wish to maintain additional information to an object.  There can be at most one annotation  per id_space for each object.   An example of an annotation would be tracking extra data not required by validation such as the name and description of a user asset.  By carefully organizing how information is organized and tracked systems can minimize the workload to only that which is necessary to perform their function.
* @note Do not use multiple inheritance with object because the code assumes a static_cast will work between object and derived types.

.. code-block:: cpp 

   class object
   {
      public:
         object(){}
         virtual ~object(){}

         static const uint8_t space_id = 0;
         static const uint8_t type_id  = 0;


         // serialized
         object_id_type          id;

         /// these methods are implemented for derived classes by inheriting abstract_object<DerivedClass>
         virtual unique_ptr<object> clone()const = 0;
         virtual void               move_from( object& obj ) = 0;
         virtual variant            to_variant()const  = 0;
         virtual vector<char>       pack()const = 0;
         virtual fc::uint128        hash()const = 0;
   };
   
   
abstract_object
------------------------------------ 
   
* Use the Curiously Recurring Template Pattern to automatically add the ability to clone, serialize, and move objects polymorphically.
* http://en.wikipedia.org/wiki/Curiously_recurring_template_pattern
   
.. code-block:: cpp 

   template<typename DerivedClass>
   class abstract_object : public object
   {
      public:
         virtual unique_ptr<object> clone()const
         {
            return unique_ptr<object>(new DerivedClass( *static_cast<const DerivedClass*>(this) ));
         }

         virtual void    move_from( object& obj )
         {
            static_cast<DerivedClass&>(*this) = std::move( static_cast<DerivedClass&>(obj) );
         }
         virtual variant to_variant()const { return variant( static_cast<const DerivedClass&>(*this), MAX_NESTING ); }
         virtual vector<char> pack()const  { return fc::raw::pack( static_cast<const DerivedClass&>(*this) ); }
         virtual fc::uint128  hash()const  {  
             auto tmp = this->pack();
             return fc::city_hash_crc_128( tmp.data(), tmp.size() );
         }
   };
   
   typedef flat_map<uint8_t, object_id_type> annotation_map;
 
   
annotated_object
-----------------------------------

* An object that is easily extended by providing pointers to other objects, one for each space.

.. code-block:: cpp 

   template<typename DerivedClass>
   class annotated_object : public abstract_object<DerivedClass>
   {
      public:
         /** return object_id_type() if no anotation is found for id_space */
         object_id_type          get_annotation( uint8_t annotation_id_space )const
         {
            auto itr = annotations.find(annotation_id_space);
            if( itr != annotations.end() ) return itr->second;
            return object_id_type();
         }
         void                    set_annotation( object_id_type id )
         {
            annotations[id.space()] = id;
         }

         /**
          *  Annotations should be accessed via get_annotation and set_annotation so
          *  that they can be maintained in sorted order.
          */
         annotation_map annotations;
   };
  

-------------------------

object_database.hpp
===============================

object_database
-----------------------------------
* maintains a set of indexed objects that can be modified with multi-level rollback support

.. code-block:: cpp 

   class object_database
   {
      public:
         object_database();
         ~object_database();

         void reset_indexes() { _index.clear(); _index.resize(255); }

         void open(const fc::path& data_dir );

         /**
          * Saves the complete state of the object_database to disk, this could take a while
          */
         void flush();
         void wipe(const fc::path& data_dir); // remove from disk
         void close();

         template<typename T, typename F>
         const T& create( F&& constructor )
         {
            auto& idx = get_mutable_index<T>();
            return static_cast<const T&>( idx.create( [&](object& o)
            {
               assert( dynamic_cast<T*>(&o) );
               constructor( static_cast<T&>(o) );
            } ));
         }

         ///These methods are used to retrieve indexes on the object_database. All public index accessors are const-access only.
         /// @{
         template<typename IndexType>
         const IndexType& get_index_type()const {
            static_assert( std::is_base_of<index,IndexType>::value, "Type must be an index type" );
            return static_cast<const IndexType&>( get_index( IndexType::object_type::space_id, IndexType::object_type::type_id ) );
         }
         template<typename T>
         const index&  get_index()const { return get_index(T::space_id,T::type_id); }
         const index&  get_index(uint8_t space_id, uint8_t type_id)const;
         const index&  get_index(object_id_type id)const { return get_index(id.space(),id.type()); }
         /// @}

         const object& get_object( object_id_type id )const;
         const object* find_object( object_id_type id )const;

         /// These methods are mutators of the object_database. You must use these methods to make changes to the object_database,
         /// in order to maintain proper undo history.
         ///@{

         const object& insert( object&& obj ) { return get_mutable_index(obj.id).insert( std::move(obj) ); }
         void          remove( const object& obj ) { get_mutable_index(obj.id).remove( obj ); }
         template<typename T, typename Lambda>
         void modify( const T& obj, const Lambda& m ) {
            get_mutable_index(obj.id).modify(obj,m);
         }

         ///@}

         template<typename T>
         static const T& cast( const object& obj )
         {
            assert( nullptr != dynamic_cast<const T*>(&obj) );
            return static_cast<const T&>(obj);
         }
         template<typename T>
         static T& cast( object& obj )
         {
            assert( nullptr != dynamic_cast<T*>(&obj) );
            return static_cast<T&>(obj);
         }

         template<typename T>
         const T& get( object_id_type id )const
         {
            const object& obj = get_object( id );
            assert( nullptr != dynamic_cast<const T*>(&obj) );
            return static_cast<const T&>(obj);
         }
         template<typename T>
         const T* find( object_id_type id )const
         {
            const object* obj = find_object( id );
            assert(  !obj || nullptr != dynamic_cast<const T*>(obj) );
            return static_cast<const T*>(obj);
         }

         template<uint8_t SpaceID, uint8_t TypeID, typename T>
         const T* find( object_id<SpaceID,TypeID,T> id )const { return find<T>(id); }

         template<uint8_t SpaceID, uint8_t TypeID, typename T>
         const T& get( object_id<SpaceID,TypeID,T> id )const { return get<T>(id); }

         template<typename IndexType>
         IndexType* add_index()
         {
            typedef typename IndexType::object_type ObjectType;
            if( _index[ObjectType::space_id].size() <= ObjectType::type_id  )
                _index[ObjectType::space_id].resize( 255 );
            assert(!_index[ObjectType::space_id][ObjectType::type_id]);
            unique_ptr<index> indexptr( new IndexType(*this) );
            _index[ObjectType::space_id][ObjectType::type_id] = std::move(indexptr);
            return static_cast<IndexType*>(_index[ObjectType::space_id][ObjectType::type_id].get());
         }

         template<typename IndexType, typename SecondaryIndexType, typename... Args>
         SecondaryIndexType* add_secondary_index( Args... args )
         {
            return get_mutable_index_type<IndexType>().template add_secondary_index<SecondaryIndexType, Args...>(args...);
         }

         void pop_undo();

         fc::path get_data_dir()const { return _data_dir; }

         /** public for testing purposes only... should be private in practice. */
         undo_database                          _undo_db;
     protected:
         template<typename IndexType>
         IndexType&    get_mutable_index_type() {
            static_assert( std::is_base_of<index,IndexType>::value, "Type must be an index type" );
            return static_cast<IndexType&>( get_mutable_index( IndexType::object_type::space_id, IndexType::object_type::type_id ) );
         }
         template<typename T>
         index& get_mutable_index()                  
		 { return get_mutable_index(T::space_id,T::type_id); }
         index& get_mutable_index(object_id_type id) 
		 { return get_mutable_index(id.space(),id.type());   }
         index& get_mutable_index(uint8_t space_id, uint8_t type_id);

     private:

         friend class base_primary_index;
         friend class undo_database;
         void save_undo( const object& obj );
         void save_undo_add( const object& obj );
         void save_undo_remove( const object& obj );

         fc::path                                 _data_dir;
         vector< vector< unique_ptr<index> > >    _index;
   };


-------------------------

object_id.hpp
===============================

.. code-block:: cpp 

   using  std::shared_ptr;
   using  std::unique_ptr;
   using  std::vector;
   using  fc::flat_map;
   using  fc::variant;
   using  fc::unsigned_int;
   using  fc::signed_int;
   
   
object_id_type
-----------------------------

.. code-block:: cpp 

   struct object_id_type
   {
      object_id_type( uint8_t s, uint8_t t, uint64_t i )
      {
         FC_ASSERT( i >> 48 == 0, "instance overflow", ("instance",i) );
         number = (uint64_t(s)<<56) | (uint64_t(t)<<48) | i;
      }
      object_id_type(){ number = 0; }

      uint8_t  space()const       { return number >> 56;              }
      uint8_t  type()const        { return number >> 48 & 0x00ff;     }
      uint16_t space_type()const { return number >> 48;              }
      uint64_t instance()const { return number & GRAPHENE_DB_MAX_INSTANCE_ID; }
      bool     is_null()const { return number == 0; }
      explicit operator uint64_t()const { return number; }

      friend bool  operator == ( const object_id_type& a, const object_id_type& b ) { return a.number == b.number; }
      friend bool  operator != ( const object_id_type& a, const object_id_type& b ) { return a.number != b.number; }
      friend bool  operator < ( const object_id_type& a, const object_id_type& b ) { return a.number < b.number; }
      friend bool  operator > ( const object_id_type& a, const object_id_type& b ) { return a.number > b.number; }

      object_id_type& operator++(int) { ++number; return *this; }
      object_id_type& operator++()    { ++number; return *this; }

      friend object_id_type operator+(const object_id_type& a, int delta ) {
         return object_id_type( a.space(), a.type(), a.instance() + delta );
      }
      friend object_id_type operator+(const object_id_type& a, int64_t delta ) {
         return object_id_type( a.space(), a.type(), a.instance() + delta );
      }
      friend size_t hash_value( object_id_type v ) { return std::hash<uint64_t>()(v.number); }

      template< typename T >
      bool is() const
      {
         return (number >> 48) == ((T::space_id << 8) | (T::type_id));
      }

      template< typename T >
      T as() const
      {
         FC_ASSERT( is<T>() );
         return T( *this );
      }

      explicit operator std::string() const
      {
          return fc::to_string(space()) + "." + fc::to_string(type()) + "." + fc::to_string(instance());
      }

      uint64_t       number;
   };
   


object_id
----------------------------------------
   
.. code-block:: cpp 

   class object;
   class object_database;

   template<uint8_t SpaceID, uint8_t TypeID, typename T = object>
   struct object_id
   {
      typedef T type;
      static const uint8_t space_id = SpaceID;
      static const uint8_t type_id = TypeID;

      object_id(){}
      object_id( unsigned_int i ):instance(i){}
      explicit object_id( uint64_t i ):instance(i)
      {
         FC_ASSERT( (i >> 48) == 0 );
      }
      object_id( object_id_type id ):instance(id.instance())
      {
      }

      friend object_id operator+(const object_id a, int64_t delta ) { return object_id( uint64_t(a.instance.value+delta) ); }
      friend object_id operator+(const object_id a, int delta ) { return object_id( uint64_t(a.instance.value+delta) ); }

      operator object_id_type()const { return object_id_type( SpaceID, TypeID, instance.value ); }
      explicit operator uint64_t()const { return object_id_type( *this ).number; }

      template<typename DB>
      const T& operator()(const DB& db)const { return db.get(*this); }

      friend bool  operator == ( const object_id& a, const object_id& b ) { return a.instance == b.instance; }
      friend bool  operator != ( const object_id& a, const object_id& b ) { return a.instance != b.instance; }
      friend bool  operator == ( const object_id_type& a, const object_id& b ) { return a == object_id_type(b); }
      friend bool  operator != ( const object_id_type& a, const object_id& b ) { return a != object_id_type(b); }
      friend bool  operator == ( const object_id& b, const object_id_type& a ) { return a == object_id_type(b); }
      friend bool  operator != ( const object_id& b, const object_id_type& a ) { return a != object_id_type(b); }

      friend bool  operator < ( const object_id& a, const object_id& b ) { return a.instance.value < b.instance.value; }
      friend bool  operator > ( const object_id& a, const object_id& b ) { return a.instance.value > b.instance.value; }

      friend size_t hash_value( object_id v ) { return std::hash<uint64_t>()(v.instance.value); }

      unsigned_int instance;
   };



-------------------------

simple_index.hpp
===============================

simple_index
------------------------------

* A simple index uses a vector<unique_ptr<T>> to store data
* This index is preferred in situations where the data will never be removed from main memory and when access by ID is the only kind of access that is necessary.
   
.. code-block:: cpp 
 
   template<typename T>
   class simple_index : public index
   {
      public:
         typedef T object_type;

         virtual const object&  create( const std::function<void(object&)>& constructor ) override
         {
             auto id = get_next_id();
             auto instance = id.instance();
             if( instance >= _objects.size() ) _objects.resize( instance + 1 );
             _objects[instance].reset(new T);
             _objects[instance]->id = id;
             constructor( *_objects[instance] );
             _objects[instance]->id = id; // just in case it changed
             use_next_id();
             return *_objects[instance];
         }

         virtual void modify( const object& obj, const std::function<void(object&)>& modify_callback ) override
         {
            assert( obj.id.instance() < _objects.size() );
            modify_callback( *_objects[obj.id.instance()] );
         }

         virtual const object& insert( object&& obj )override
         {
            auto instance = obj.id.instance();
            assert( nullptr != dynamic_cast<T*>(&obj) );
            if( _objects.size() <= instance ) _objects.resize( instance+1 );
            assert( !_objects[instance] );
            _objects[instance].reset( new T( std::move( static_cast<T&>(obj) ) ) );
            return *_objects[instance];
         }

         virtual void remove( const object& obj ) override
         {
            assert( nullptr != dynamic_cast<const T*>(&obj) );
            const auto instance = obj.id.instance();
            _objects[instance].reset();
            while( (_objects.size() > 0) && (_objects.back() == nullptr) )
               _objects.pop_back();
         }

         virtual const object* find( object_id_type id )const override
         {
            assert( id.space() == T::space_id );
            assert( id.type() == T::type_id );

            const auto instance = id.instance();
            if( instance >= _objects.size() ) return nullptr;
            return _objects[instance].get();
         }

         virtual void inspect_all_objects(std::function<void (const object&)> inspector)const override
         {
            try {
               for( const auto& ptr : _objects )
               {
                  if( ptr.get() )
                     inspector(*ptr);
               }
            } FC_CAPTURE_AND_RETHROW()
         }
         virtual fc::uint128 hash()const override {
            fc::uint128 result;
            for( const auto& ptr : _objects )
               result += ptr->hash();

            return result;
         }

         class const_iterator
         {
            public:
               const_iterator( const vector<unique_ptr<object>>& objects ):_objects(objects) {}
               const_iterator(
                  const vector<unique_ptr<object>>& objects,
                  const vector<unique_ptr<object>>::const_iterator& a ):_itr(a),_objects(objects){}
               friend bool operator==( const const_iterator& a, const const_iterator& b ) { return a._itr == b._itr; }
               friend bool operator!=( const const_iterator& a, const const_iterator& b ) { return a._itr != b._itr; }
               const T& operator*()const { return static_cast<const T&>(*_itr->get()); }
               const_iterator operator++(int)     // postfix
               {
                  const_iterator result( *this );
                  ++(*this);
                  return result;
               }
               const_iterator& operator++()       // prefix
               {
                  ++_itr;
                  while( (_itr != _objects.end()) && ( (*_itr) == nullptr ) )
                     ++_itr;
                  return *this;
               }
               typedef std::forward_iterator_tag iterator_category;
               typedef vector<unique_ptr<object> >::value_type value_type;
               typedef vector<unique_ptr<object> >::difference_type difference_type;
               typedef vector<unique_ptr<object> >::pointer pointer;
               typedef vector<unique_ptr<object> >::reference reference;
            private:
               vector<unique_ptr<object>>::const_iterator _itr;
               const vector<unique_ptr<object>>& _objects;
         };
         const_iterator begin()const { return const_iterator(_objects, _objects.begin()); }
         const_iterator end()const   { return const_iterator(_objects, _objects.end());   }

         size_t size()const { return _objects.size(); }
      private:
         vector< unique_ptr<object> > _objects;
   };
 

-------------------------
 
undu_database.hpp
===============================

undo_state
-------------------------------

.. code-block:: cpp 

   using std::unordered_map;
   using fc::flat_set;
   class object_database;

   struct undo_state
   {
      unordered_map<object_id_type, unique_ptr<object> > old_values;
      unordered_map<object_id_type, object_id_type>      old_index_next_ids;
      std::unordered_set<object_id_type>                 new_ids;
      unordered_map<object_id_type, unique_ptr<object> > removed;
   };


undo_database
---------------------------
* tracks changes to the state and allows changes to be undone


.. code-block:: cpp 

   class undo_database
   {
      public:
         undo_database( object_database& db ):_db(db){}

         class session
         {
            public:
               session( session&& mv )
               :_db(mv._db),_apply_undo(mv._apply_undo)
               {
                  mv._apply_undo = false;
               }
               ~session() {
                  try {
                     if( _apply_undo ) _db.undo();
                  }
                  catch ( const fc::exception& e )
                  {
                     elog( "${e}", ("e",e.to_detail_string() ) );
                     throw; // maybe crash..
                  }
                  if( _disable_on_exit ) _db.disable();
               }
               void commit() { _apply_undo = false; _db.commit();  }
               void undo()   { if( _apply_undo ) _db.undo(); _apply_undo = false; }
               void merge()  { if( _apply_undo ) _db.merge(); _apply_undo = false; }

               session& operator = ( session&& mv )
               { try {
                  if( this == &mv ) return *this;
                  if( _apply_undo ) _db.undo();
                  _apply_undo = mv._apply_undo;
                  mv._apply_undo = false;
                  return *this;
               } FC_CAPTURE_AND_RETHROW() }

            private:
               friend class undo_database;
               session(undo_database& db, bool disable_on_exit = false): _db(db),_disable_on_exit(disable_on_exit) {}
               undo_database& _db;
               bool _apply_undo = true;
               bool _disable_on_exit = false;
         };

         void    disable();
         void    enable();
         bool    enabled()const { return !_disabled; }

         session start_undo_session( bool force_enable = false );
         /**
          * This should be called just after an object is created
          */
         void on_create( const object& obj );
         /**
          * This should be called just before an object is modified
          *
          * If it's a new object as of this undo state, its pre-modification value is not stored, because prior to this
          * undo state, it did not exist. Any modifications in this undo state are irrelevant, as the object will simply
          * be removed if we undo.
          */
         void on_modify( const object& obj );
         /**
          * This should be called just before an object is removed.
          *
          * If it's a new object as of this undo state, its pre-removal value is not stored, because prior to this undo
          * state, it did not exist. Now that it's been removed, it doesn't exist again, so nothing has happened.
          * Instead, remove it from the list of newly created objects (which must be deleted if we undo), as we don't
          * want to re-delete it if this state is undone.
          */
         void on_remove( const object& obj );

         /**
          *  Removes the last committed session,
          *  note... this is dangerous if there are
          *  active sessions... thus active sessions should
          *  track
          */
         void pop_commit();

         std::size_t size()const { return _stack.size(); }
         void set_max_size(size_t new_max_size) { _max_size = new_max_size; }
         size_t max_size()const { return _max_size; }

         const undo_state& head()const;

      private:
         void undo();
         void merge();
         void commit();

         uint32_t                _active_sessions = 0;
         bool                    _disabled = true;
         std::deque<undo_state>  _stack;
         object_database&        _db;
         size_t                  _max_size = 256;
   };
   
   











|