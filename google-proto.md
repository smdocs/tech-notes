# Google Protobuf

## Building protobuf

```
sudo apt-get install autoconf automake libtool curl make g++ unzip -y
git clone https://github.com/google/protobuf.git
cd protobuf
git submodule update --init --recursive
./autogen.sh
make
make check
sudo make install
sudo ldconfig
```
