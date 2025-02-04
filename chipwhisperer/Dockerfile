FROM ubuntu:20.04

RUN apt-get update
# Avoid problem with tzdata install...
# Sets timezone to UTC though
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata

# Install dependencies
RUN apt-get install -y python3 python3-pip python3-tk wget git pkg-config libfreetype6-dev libpng-dev libusb-1.0.0 libusb-1.0.0-dev
RUN apt-get install -y avr-libc gcc-avr gcc-arm-none-eabi gfortran libopenblas-dev liblapack-dev

# Download chipwhisperer
RUN mkdir -p /opt/chipwhisperer
WORKDIR /opt/chipwhisperer
RUN git clone --recursive --depth=1 https://github.com/newaetech/chipwhisperer.git

# Install chipwhisperer
WORKDIR /opt/chipwhisperer/chipwhisperer/
# Dependencies with right version numbers
RUN pip3 install "pandas<1.1" "numpy<1.21" "matplotlib<3.4" "bokeh<3.0"

RUN pip3 install -r software/requirements.txt 
RUN python3 setup.py develop

# Install jupyter and the jupyter dependencies
WORKDIR /opt/chipwhisperer/chipwhisperer/jupyter
RUN pip3 install -r requirements.txt

# Copy udev rules
COPY 99-newae.rules /etc/udev/rules.d/99-newae.rules

COPY startup.sh /
# Create workspace directory (This is where we mount user data)
RUN mkdir -p /cw_workspace
RUN useradd -m user -G dialout,plugdev
RUN mkdir /devacm
RUN mknod -m a+rw /devacm/ttyACM0 c 166 0
RUN mknod -m a+rw /devacm/ttyACM1 c 166 1
RUN mknod -m a+rw /devacm/ttyACM2 c 166 2
USER user
# Entrypoint is directly the jupyter notebook
CMD /startup.sh
