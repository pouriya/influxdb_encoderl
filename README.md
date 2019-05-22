# `influxdb_encoderl`
InfluxDB line encoder in Erlang.  


## Build
```sh
~ $ git clone --depth 1 git/address/of/influxdb_encoderl && cd influxdb_encoderl
...
~/influxdb_encoderl $ make
```

## Usage
```sh
~/influxdb_encoderl $ make shell
Erlang/OTP 21 [erts-10.0] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:256] [hipe]
Eshell V10.0  (abort with ^G)
```
```erlang
%                               Yields iolist:
(influxdb_encoderl@localhost)1> influxdb_encoderl:encode({measurement, 10}).
["measurement"," ",["value=",["10",[]]],[],"\n"]

%                               Yields string:
(influxdb_encoderl@localhost)2> influxdb_encoderl:encode({measurement, 10}, #{return_type => string}).
"measurement value=10\n"

%                               Yields binary and puts "i" at the end of integers:
(influxdb_encoderl@localhost)3> influxdb_encoderl:encode({measurement, 10}, #{return_type => binary, encode_integer => true}).
<<"measurement value=10i\n">>

%                               Sets timestamp in nanoseconds:
(influxdb_encoderl@localhost)4> influxdb_encoderl:encode({measurement, 10}, #{return_type => string, set_timestamp => true}).
"measurement value=10 1558541063632263000\n"

(influxdb_encoderl@localhost)5> Fields = #{field1 => 10, "field2" => 3.14, <<"field3">> => true, <<"field4">> => "foo"}.
#{field1 => 10,"field2" => 3.14,<<"field3">> => true,<<"field4">> => "foo"}

(influxdb_encoderl@localhost)6> Tags = #{host => node(), ip => "127.0.0.1"}.                                          
#{host => influxdb_encoderl@localhost,ip => "127.0.0.1"}

(influxdb_encoderl@localhost)7> influxdb_encoderl:encode({measurement, Fields, Tags}, #{return_type => string, set_timestamp => true}).
"measurement,host=influxdb_encoderl@localhost,ip=127.0.0.1 field1=10,field2=3.14,field3=t,field4=foo 1558541138494154000\n"

%                               Also you can use proplists for fields or tags:
(influxdb_encoderl@localhost)8> influxdb_encoderl:encode({measurement, [{key, value}], [{tag, tag_value}]}, #{return_type => string, set_timestamp => true}).
"measurement,tag=tag_value key=value 1558541176006919000\n"

%                                         Encodes list of measurements:
(influxdb_encoderl@localhost)9> io:format(influxdb_encoderl:encode([{measurement, X ,[{node, node()}]} || X <- lists:seq(1, 100, 10)], #{return_type => string, set_timestamp => true})).
measurement,node=influxdb_encoderl@localhost value=1 1558541268990302000
measurement,node=influxdb_encoderl@localhost value=11 1558541268990314000
measurement,node=influxdb_encoderl@localhost value=21 1558541268990321000
measurement,node=influxdb_encoderl@localhost value=31 1558541268990326000
measurement,node=influxdb_encoderl@localhost value=41 1558541268990331000
measurement,node=influxdb_encoderl@localhost value=51 1558541268990336000
measurement,node=influxdb_encoderl@localhost value=61 1558541268990342000
measurement,node=influxdb_encoderl@localhost value=71 1558541268990411000
measurement,node=influxdb_encoderl@localhost value=81 1558541268990417000
measurement,node=influxdb_encoderl@localhost value=91 1558541268990422000
ok
```


#### Author
`pouriya.jahanbakhsh@gmail.com`
