# Building and running Mesos with custom allocation/hook module

1. Build Mesos with unbundled dependencies. From Mesos root folder execute:
./configure --with-glog=/usr --with-protobuf=/usr --with-boost=/usr
make
make install

You may need to install such third party packages prior to configurating:<br>
boost  
JDK  
python  
libtool  
zookeeper  
leveldb  
glog  
protoc
gmock  
curl  
sasl  
zlib  
apr  
svn  

2. Build Go allocation module. From the module root folder execute:
./bootstrap
mkdir build && cd build
../configure --with-mesos=/path/to/mesos/installation
make
make install

3. Run Mesos master from Mesos root folder with Go allocation/hook module. You may specify Go server host and port (default values are localhost:4050):
export LD_LIBRARY_PATH=/usr/local/lib
./bin/mesos-master.sh --ip=127.0.0.1 --work_dir=./mesos_master --allocator=org_apache_mesos_GoHierarchicalAllocator --modules='{"libraries":[{"file":"/usr/local/lib/mesos/libgoallocation.so", "modules":[{"name":"org_apache_mesos_GoHierarchicalAllocator"},"parameters": [{"key": "host","value": "localhost"},{"key": "port","value": "4050"}]]}]}'
./bin/mesos-master.sh --ip=127.0.0.1 --work_dir=./mesos_master --hooks=org_apache_mesos_GoHook --modules='{"libraries":[{"file":"/usr/local/lib/mesos/libgoallocation.so", "modules":[{"name":"org_apache_mesos_GoHook", "parameters": [{"key": "host","value": "localhost"},{"key": "port","value": "4050"}]}]}]}'

4. Run Mesos slave from Mesos root folder:
export LD_LIBRARY_PATH=/usr/local/lib
./bin/mesos-slave.sh --master=127.0.0.1:5050


List of events Hook module sends to Go server:
  1. MasterLaunchTaskLabelDecorator  
    Protobuf sent to server: MasterLaunchTaskLabelDecorator  
    Protobuf expected from server: Labels  
  2. SlaveRunTaskLabelDecorator  
    Protobuf sent to server: SlaveRunTaskLabelDecorator  
    Protobuf expected from server: Labels  
  3. SlaveExecutorEnvironmentDecorator  
    Protobuf sent to server: SlaveExecutorEnvironmentDecorator  
    Protobuf expected from server: Environment  
  4. SlaveRemoveExecutorHook  
    Protobuf sent to server: SlaveRemoveExecutorHook  
    Protobuf expected from server: None. Server should locate the file created by environment decorator hook<br>
  and delete it

List of events Allocator module sends to Go server:  
  AddFramework  
  RemoveFramework  
  ActivateFramework  
  DeactivateFramework  
  AddSlave  
  RemoveSlave  
  UpdateSlave  
  ActivateSlave  
  DeactivateSlave  
  UpdateWhitelist  
  RequestResources  
  UpdateAllocation  
  RecoverResources  
  ReviveOffers  
  UpdateFramework  
  Each event sends and expects a protobuf with event's name.


