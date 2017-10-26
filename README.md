# NTLM Proxy Setup for Go

# Example

```Go
package main

import (
	"context"
	"fmt"
	"io/ioutil"
	"net"
	"net/http"
	"time"

	"github.com/anynines/go-proxy-setup-ntlm/proxysetup/ntlm"
)

func main() {
	proxyAddr := "10.0.0.45:3128"
	url := "https://api.aws.ie.a9s.eu/v2/info"

	dialContext := (&net.Dialer{
		KeepAlive: 30 * time.Second,
		Timeout:   30 * time.Second,
	}).DialContext

	ntlmDialContext := func(ctx context.Context, network, address string) (net.Conn, error) {
		conn, err := dialContext(ctx, network, proxyAddr)
		if err != nil {
			return conn, err
		}
		fmt.Println("Injecting NTLM code.")
		err = ntlm.ProxySetup(conn, address)
		if err != nil {
			fmt.Printf("Failed to inject NTLM code: %v.", err)
			return conn, err
		}
		return conn, err
	}

	tr := &http.Transport{
		Proxy:       nil,
		DialContext: ntlmDialContext,
	}

	c := &http.Client{
		Transport: tr,
	}

	response, err := c.Get(url)
	fmt.Printf("response = %v\n", response)
	if err != nil {
		fmt.Printf("err = %v\n", err)
		return
	}

	defer response.Body.Close()
	body, err := ioutil.ReadAll(response.Body)
	if err != nil {
		fmt.Printf("err = %v\n", err)
		return
	}
	fmt.Printf("body= %v\n", string(body))

	fmt.Println("exit")
}
```
