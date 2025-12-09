# Path Traversal

![photo](https://github.com/t5nix/write-up/blob/main/assets/pwn.png?raw=true)

---

## 1. Discovering the Vulnerable Endpoint

While exploring the application, we find the endpoint:

`/api/wordlist/download`

```go
apiGroup := router.Group("/api")
{
    wordlistGroup := apiGroup.Group("/wordlist")
    {
        wordlistGroup.GET("", controllers.HandleListWordlist)
        wordlistGroup.POST("/download", controllers.HandleDownloadWordlist)
        wordlistGroup.POST("", controllers.HandleUploadWordlist)
        wordlistGroup.DELETE("", controllers.HandleDeleteWordlist)
    }
    bruteforceGroup := apiGroup.Group("/bruteforce")
    {
        bruteforceGroup.POST("", controllers.StartBruteforce)
    }
}
```

This endpoint allows file downloads, but without proper validation of the `filename` parameter.

---

## 2. Exploiting the Path Traversal Vulnerability

By injecting `../../../` sequences, we can traverse up the directory structure and read arbitrary files:

![photo](https://github.com/t5nix/write-up/blob/main/assets/path.png?raw=true)

This opens the door to reading sensitive system files.

---

## 3. Reading `/proc/self/environ`

The `/proc/self/environ` file contains the environment variables of the currently running process in the container (`self` corresponds to the current process PID).

This is an interesting target as it can reveal secrets.

![photo](https://github.com/t5nix/write-up/blob/main/assets/environ.png?raw=true)

---

## 4. Flag

And there we have it, we obtain the flag:

`Hero{e9e2b63a0daa9ee41d2133b450425b2cd7c7510e5a28b655748456bd3f6e5c2a}`
