Go interface for STUN/TURN servers
=======
This is a library providing a Go interface compatible with the golang
[proxy](https://golang.org/x/net/proxy) package which connects through a
[TURN](https://tools.ietf.org/html/rfc5766) relay.

This package provides parsing and encoding support for [STUN](https://tools.ietf.org/html/rfc5389)
and [TURN](https://tools.ietf.org/html/rfc5766) protocols.

Installation
------------

```golang
go get github.com/yuriy-klerk/stungo
```

Example
------------

```golang
package main

import (
	"io/ioutil"
	"log"
	"net"
	"net/http"

	"https://github.com/yuriy-klerk/stungo/client"
)

func main() {
	// Connect to the stun/turn server
	conn, err := net.Dial("tcp", "0.0.0.0:19302")
	if err != nil {
		log.Fatal("error dialing TURN server: ", err)
	}
	defer conn.Close()

	credentials := client.LongtermCredentials("username", "password")
	dialer, err := client.NewDialer(&credentials, conn)
	if err != nil {
		log.Fatal("failed to obtain dialer: ", err)
	}

	httpClient := &http.Client{Transport: &http.Transport{Dial: dialer.Dial}}
	httpResp, err := httpClient.Get("http://www.example.com/")
	if err != nil {
		log.Fatal("error performing http request: ", err)
	}
	defer httpResp.Body.Close()

	httpBody, err := ioutil.ReadAll(httpResp.Body)
	if err != nil {
		log.Fatal("error reading http response: ", err)
	}
	log.Printf("received %d bytes", len(httpBody))
}
```
## Products that use stungo

[![Alt text](https://callaba.io/img/logo-black.svg)](https://callaba.io/)
