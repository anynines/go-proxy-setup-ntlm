# NTLM Proxy Setup for Go

# Example

```Go
package main

import (
	"fmt"
	"net/http"

	"github.com/anynines/go-proxy-setup-ntlm/proxysetup/ntlm"
)

func main() {
	tr := http.Transport{
		ProxySetup: ntlm.ProxySetup,
	}
	client := &http.Client{Transport: &tr}
	resp, err := client.Get("https://www.google.de")
	if err != nil {
		fmt.Println(err)
		fmt.Println("Error.")
		return
	}
	fmt.Println(resp)

	fmt.Println("Done.")
}
```
