boost

Boost provides free peer-reviewed portable C++ source libraries.

Run the following commands to clone the repo:
```
git clone --recurse-submodules git@github.com:etbala/boostport.git
```
```
cd boostport
```
```
git submodule update --init --recursive
```

If git submodule update --init --recursive returns an error, try removing the boost path using
```
rm -r boost
```
then running 
```
git clone --recurse-submodules git@github.com:boostorg/boost.git
```
