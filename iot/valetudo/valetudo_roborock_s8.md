# Valetudo on Roborock S8

## References

- [Howto root the Roborock S8](https://builder.dontvacuum.me/nextgen/roborock_s8.pdf) from https://builder.dontvacuum.me

## Preparation

The laptop used for the rooting is running Debian 13 (Trixie/testing).

### sunxi-livesuite

- Used to upload firmware images to the robot.
- Download from [https://github.com/linux-sunxi/sunxi-livesuite](https://github.com/linux-sunxi/sunxi-livesuite).
- Depends on `libpng12.so.0`. This is a very old version that was last available in Debian 8. The library can be easily compiled on Debian 13.
- It also contains a kernel module required to connect the robot to the computer.
- The module sources are only compatible with kernel versions <6. They have to be modified to allow compilation.
```shell
# Download the sources

mkdir ~/valetudo
cd ~/valetudo
git clone https://github.com/linux-sunxi/sunxi-livesuite.git
cd sunxi-livesuite

# Install the requirements
sudo apt install build-essential linux-headers-6.6.15-amd64

# Compile the kernel module
cd awusb
sed -i 's/SUBDIRS=/M=/g' Makefile
make
sudo insmod awusb.ko

# Download the libpng12 sources
cd ~/valetudo
git clone https://github.com/pnggroup/libpng.git
cd libpng
git switch libpng12

# Compilke and install libpng12
./configure --prefix=/opt/libpng12
make
sudo make install

# Modify the LiveSuite script to use the compiled libpng12
cd ~/valetudo/sunxi-livesuite
sed -i 's%^LD_LIBRARY_PATH=%LD_LIBRARY_PATH=/opt/libpng12/lib:%g' LiveSuit.sh
```

LiveSuite should now be usable:
```shell
./LiveSuit.sh
```

