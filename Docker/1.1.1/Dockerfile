FROM jdye64/docker-hwx:nifi-1.0.0

ENV LD_LIBRARY_PATH = $LD_LIBRARY_PATH:/opencv-3.1.0/build/lib/


# Update apt-get repository
RUN apt-get update

# Make sure mvn is intalled
RUN wget http://www-us.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
RUN tar -zxf apache-maven-3.3.9-bin.tar.gz
RUN cp -R apache-maven-3.3.9 /usr/local
RUN ln -s /usr/local/apache-maven-3.3.9/bin/mvn /usr/bin/mvn
RUN export M2_HOME=/usr/local/apache-maven-3.3.9
RUN echo "Maven is on version `mvn -v`"

# Install system dependencies
RUN apt-get install -y unzip ant wget curl

# Install Tesseract dependencies and the English language support
RUN apt-get update && apt-get install -y tesseract-ocr && apt-get install -y tesseract-ocr-eng

# OpenCV System compiler dependencies
RUN apt-get install -y build-essential

# OpenCV required dependencies
RUN apt-get install -y cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev curl wget

# OpenCV optional dependencies
RUN apt-get install -y python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

# Download the OpenCV source code
RUN wget https://github.com/Itseez/opencv/archive/3.1.0.zip

RUN unzip 3.1.0.zip
RUN cd opencv-3.1.0 && mkdir build && cd build && cmake -DWITH_IPP=OFF -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -DBUILD_SHARED_LIBS=OFF .. && make -j8

# Since we can't use Docker to get outside of the Docker build context AKA ../../Processors. Lets just pull things down from GitHub
RUN git clone https://github.com/jasper2xF/nifi-addons.git

# Compiles all of the NiFi addon custom processors
RUN cd /nifi-addons/Processors && mvn clean install package -DskipTests

# Move all of the nifi-addon nars to the NiFi lib directory.
RUN mv /nifi-addons/Processors/nifi-barcode/nifi-barcode-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-docker/nifi-docker-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-file/nifi-file-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-jmx/nifi-jmx-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-ocr/nifi-ocr-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-opencv/nifi-opencv-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-salesforce/nifi-salesforce-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-spinx/nifi-spinx-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-tesseract/nifi-tesseract-nar/target/*.nar $NIFI_HOME/lib/.
RUN mv /nifi-addons/Processors/nifi-websockets/nifi-websockets-nar/target/*.nar $NIFI_HOME/lib/.

# Remove the /nifi-addons directory to trim the fat from the Docker image
RUN rm -rf /nifi-addons
RUN rm -rf ~/.m2