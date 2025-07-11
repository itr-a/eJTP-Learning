# Netcat

### Command-line utility for reading and writing data between two computer networks

**Basic syntax**
```bash
nc [options] <host> <port>
```

|Option|Type|Description|
|---|---|---|
|-4|Protocol|Use IPv4 only|
|-6|Protocol|Use IPv6 only|
|-U|Protocol|Use Unix domain sockets|
|-u|Protocol|Use UDP connection|
|-g <hop1,hop2...>|Connect mode|Set hops for loose routing in IPv4. Hops are IP or hostname|
|-p <port>|Connect mode|Binds the Netcat source port to <port>|
|