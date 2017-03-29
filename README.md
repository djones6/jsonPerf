# jsonPerf

Kitura-based JSON serialization performance testcase.  A number of routes are provided for testing the performance of serializing `Int`, `UInt`, `Double` and `Float` number types with either a default value, or one that you provide. There are also routes for `Bool` and purely `String` types.

The serialized payload is configurable via the following environment variables, which should be set prior to launching the application:

- `TESTVALUE`: sets the number to be serialized. (default 1234)
- `TESTCOUNT`: sets the number of elements in the dictionary being serialized. (default 10)

## Steps for measuring using the 'Kitura-Bench' scripts

1. Build the project
```
git clone https://github.com/djones6/jsonPerf ~/jsonPerf
cd ~/jsonPerf
swiftenv local <baseline swift build>
swift build -c release --build-path=baselineBuild
swiftenv local <new swift build>
swift build -c release --build-path=newBuild
```

2. Get the driver scripts
```
git clone https://github.com/djones6/Kitura-Bench ~/kiturabench
```

3. Get the 'wrk' tool (my fork, 'interval' branch adds the -i option to provide periodic throughput measurements)
```
git clone https://github.com/djones6/wrk.git -b interval
cd wrk
make
```
This will build wrk, then you need to put it on your path:
```
export PATH=$PATH:$HOME/wrk
```
(you could add this to your `.bashrc`)

4. Run a compare:
```
env TESTVALUE=123456 TESTCOUNT=20 URL=http://localhost:8080/jsonInt ITERATIONS=5 DURATION=60 ~/kiturabench/compare.sh ~/jsonPerf/baselineBuild/release/JSON ~/jsonPerf/newBuild/release/JSON
```

If you want to test your builds first, just launch the binary you built and then point your browser to eg: http://localhost:8080/jsonInt

You can launch it manually like this:
`env TESTVALUE=123456 TESTCOUNT=20 newBuild/release/JSON`
