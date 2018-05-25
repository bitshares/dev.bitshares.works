## Boost Versions 

### Supported Versions

* Boost: between 1.57 and 1.63

Versions earlier than 1.57 or newer than 1.63 are NOT supported. If your system Boost version is newer, then you will need to manually build an older version of Boost and specify it to CMake using -DBOOST_ROOT. Example:

    cmake -DBOOST_ROOT=~/boost160 .

***

### Known Issues

#### bitshares-core

- If built with **boost 1.60** (and perhaps other versions), some parameter styles in command line is not supported. #881
(e.g.)
   - `./cli_wallet -s ws://1.2.3.4:5678/` works with boost 1.57 and 1.58, but doesn't work with 1.60
   - `./cli_wallet -sws://1.2.3.4:5678/` works with boost 1.57, 1.58 and 1.60
   
- `time_point_sec` internally uses an unsigned int to count seconds from epoch, like unix time. That means it is impossible to represent dates before 1970-01-01. Still, we sometime see dates before that in JSON output. Probably a bug in boost::posix_time, have received a report that it's fixed in Boost-1.64 #597
   - Updating to boost 1.64 might fix this issue, however BitShares is not compatible with Boost 1.64 at this point (typing:4/29/2018)


#### bitshares-fc


***

(ref)
- fc::time_point_sec::to_iso_string is broken #597 (bitshares-core)
- Upgrade to Boost 1.65 #36 (bitshares-fc)
- Issue 727b - A second option #33 (bitshares-fc)
