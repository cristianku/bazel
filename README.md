# bazel
Bazel compiled for raspberry pi zero

Here in this repository you find the binary already compiled

need to download bazel and put it in /usr/local/bin

By the way I have followed this procedure :

$ sudo apt-get install pkg-config zip g++ zlib1g-dev unzip oracle-java8-jdk

$ sudo apt-get install gcc-4.8 g++-4.8
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 100
$ sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 100


1. Install Protobuf

I cloned the repository:

$ git clone https://github.com/google/protobuf.git
and built it:

$ cd protobuf
$ git checkout v3.1.0
$ ./autogen.sh
$ ./configure
$ make CXX=g++-4.8 -j 4
$ sudo make install
$ sudo ldconfig
The build took less than 1 hour to finish.

I could see the version of installed protobuf like:

$ protoc --version

libprotoc 3.1.0
2. Install Bazel

a. download

I got the latest release from here, and unzipped it:

$ wget https://github.com/bazelbuild/bazel/releases/download/0.5.1/bazel-0.5.1-dist.zip
$ unzip -d bazel bazel-0.5.1-dist.zip
b. edit bootstrap files

In the unzipped directory, I opened the scripts/bootstrap/compile.sh file:

$ cd bazel
$ vi scripts/bootstrap/compile.sh
searched for lines that looked like following:

run "${JAVAC}" -classpath "${classpath}" -sourcepath "${sourcepath}" \
      -d "${output}/classes" -source "$JAVA_VERSION" -target "$JAVA_VERSION" \
      -encoding UTF-8 "@${paramfile}"
and appended -J-Xmx500M to the last line so that the whole lines would look like:

run "${JAVAC}" -classpath "${classpath}" -sourcepath "${sourcepath}" \
      -d "${output}/classes" -source "$JAVA_VERSION" -target "$JAVA_VERSION" \
      -encoding UTF-8 "@${paramfile}" -J-Xmx500M
It was for enlarging the max heap size of Java.

Finally, we have to add one thing to tools/cpp/cc_configure.bzl - open it up for editing:

nano tools/cpp/cc_configure.bzl
Place the line return "arm" around line 133 (at the beginning of the _get_cpu_value function):

...
"""Compute the cpu_value based on the OS name."""
return "arm"
...

c. compile

After that, started building with:

$ chmod u+w ./* -R
$ sudo ./compile.sh


When the build finishes, you end up with a new binary, output/bazel. Copy that to your /usr/local/bin directory.

sudo cp output/bazel /usr/local/bin/bazel
