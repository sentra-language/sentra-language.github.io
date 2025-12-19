---
layout: default
title: Examples
nav_order: 5
---

# Code Examples
{: .fs-9 }

Real-world examples demonstrating Sentra's capabilities.
{: .fs-6 .fw-300 }

---

## Security Tools

### Port Scanner

```sentra
// Simple port scanner
fn scan_host(host, start_port, end_port) {
    log("Scanning " + host + " ports " + str(start_port) + "-" + str(end_port))
    
    let results = port_scan(host, start_port, end_port, "TCP")
    let open_ports = []
    
    for (let port in results) {
        if (port["state"] == "open") {
            push(open_ports, port["port"])
            log("  Port " + str(port["port"]) + " OPEN - " + port["service"])
        }
    }
    
    return open_ports
}

// Usage
let ports = scan_host("127.0.0.1", 20, 100)
log("Found " + str(len(ports)) + " open ports")
```

### Web Vulnerability Scanner

```sentra
// Check for common web vulnerabilities
fn scan_website(url) {
    log("Scanning " + url)
    let issues = []
    
    // Check security headers
    let response = http_get(url)
    let headers = response["headers"]
    
    if (!headers["X-Frame-Options"]) {
        push(issues, "Missing X-Frame-Options header")
    }
    
    if (!headers["Content-Security-Policy"]) {
        push(issues, "Missing CSP header")
    }
    
    if (len(issues) > 0) {
        log("Security issues found:")
        for (let issue in issues) {
            log("  - " + issue)
        }
    } else {
        log("No issues found")
    }
    
    return issues
}

scan_website("https://example.com")
```

---

## Network Programming

### HTTP Server

```sentra
// Create a REST API server
let server = http_server_create("127.0.0.1", 8080)

// Health check endpoint
http_server_route(server["id"], "GET", "/health", fn(req) {
    return http_response(200, '{"status": "healthy"}', {
        "Content-Type": "application/json"
    })
})

// Data endpoint
http_server_route(server["id"], "POST", "/data", fn(req) {
    log("Received data: " + req["body"])
    return http_response(200, '{"received": true}', {
        "Content-Type": "application/json"
    })
})

http_server_start(server["id"])
log("Server running at http://127.0.0.1:8080")

// Keep server running
while (true) {
    sleep(1000)
}
```

### WebSocket Chat Server

```sentra
// Simple WebSocket chat server
let server = ws_listen("127.0.0.1", 8765)
log("WebSocket server running on ws://127.0.0.1:8765")

let clients = {}

while (true) {
    // Accept new connections
    let client = ws_server_accept(server["id"], 1)
    if (client) {
        clients[client["id"]] = client
        ws_server_send_to(server["id"], client["id"], "Welcome to chat!")
        log("Client connected: " + client["id"])
    }
    
    // Handle messages
    for (let id in clients) {
        let msg = ws_server_receive_from(server["id"], id)
        if (msg) {
            log("Broadcasting: " + msg)
            ws_server_broadcast(server["id"], "[User]: " + msg)
        }
    }
    
    sleep(100)
}
```

---

## Data Processing

### Log Analyzer

```sentra
// Analyze security logs
fn analyze_logs(filename) {
    if (!file_exists(filename)) {
        log("File not found: " + filename)
        return
    }
    
    let content = file_read(filename)
    let lines = split(content, "\n")
    let stats = {
        "total": 0,
        "errors": 0,
        "warnings": 0,
        "failed_logins": 0
    }
    
    for (let line in lines) {
        stats["total"] = stats["total"] + 1
        
        if (contains(line, "ERROR")) {
            stats["errors"] = stats["errors"] + 1
        }
        if (contains(line, "WARNING")) {
            stats["warnings"] = stats["warnings"] + 1
        }
        if (contains(line, "Failed login")) {
            stats["failed_logins"] = stats["failed_logins"] + 1
        }
    }
    
    log("Log Analysis Results:")
    log("  Total lines: " + str(stats["total"]))
    log("  Errors: " + str(stats["errors"]))
    log("  Warnings: " + str(stats["warnings"]))
    log("  Failed logins: " + str(stats["failed_logins"]))
    
    return stats
}

analyze_logs("/var/log/auth.log")
```

---

## Algorithms

Sentra includes 40+ classic algorithm implementations demonstrating the language's capabilities.

### Two Sum (Hash Table)

```sentra
fn twoSum(nums, target) {
    let seen = {}
    for (let i in range(0, len(nums))) {
        let complement = target - nums[i]
        if (seen[str(complement)] != nil) {
            return [seen[str(complement)], i]
        }
        seen[str(nums[i])] = i
    }
    return []
}

log(twoSum([2, 7, 11, 15], 9))  // [0, 1]
```

### Quick Sort

```sentra
fn quickSort(arr, low, high) {
    if (low < high) {
        let pivot = arr[high]
        let i = low - 1

        for (let j in range(low, high)) {
            if (arr[j] <= pivot) {
                i = i + 1
                let temp = arr[i]
                arr[i] = arr[j]
                arr[j] = temp
            }
        }

        let temp = arr[i + 1]
        arr[i + 1] = arr[high]
        arr[high] = temp
        let pi = i + 1

        quickSort(arr, low, pi - 1)
        quickSort(arr, pi + 1, high)
    }
    return arr
}

let arr = [64, 34, 25, 12, 22, 11, 90]
log(quickSort(arr, 0, len(arr) - 1))  // [11, 12, 22, 25, 34, 64, 90]
```

### Dynamic Programming (Coin Change)

```sentra
fn coinChange(coins, amount) {
    let dp = [amount + 1]
    for (let i in range(0, amount + 1)) {
        dp[i] = amount + 1
    }
    dp[0] = 0

    for (let i in range(1, amount + 1)) {
        for (let coin in coins) {
            if (coin <= i && dp[i - coin] + 1 < dp[i]) {
                dp[i] = dp[i - coin] + 1
            }
        }
    }

    if (dp[amount] > amount) {
        return -1
    }
    return dp[amount]
}

log(coinChange([1, 2, 5], 11))  // 3 (5+5+1)
```

### Algorithm Categories

The full [leetcode.sn](https://github.com/sentra-language/sentra/blob/main/examples/algorithms/leetcode.sn) includes:

| Category | Algorithms |
|:---------|:-----------|
| **Hash Tables** | Two Sum, Contains Duplicate, Single Number |
| **Two Pointers** | Container With Most Water, Trapping Rain Water |
| **Binary Search** | Search Insert, Sqrt(x), Find Min in Rotated Array |
| **Dynamic Programming** | Climbing Stairs, House Robber, Coin Change, LIS |
| **Greedy** | Jump Game, Best Time to Buy/Sell Stock |
| **Arrays** | Move Zeroes, Rotate Array, Product Except Self |
| **Math** | Palindrome, Happy Number, Power of Two, GCD |
| **Sorting** | Quick Sort, Merge Sorted Arrays |

---

## More Examples

Find comprehensive examples in the [GitHub repository](https://github.com/sentra-language/sentra/tree/main/examples):

- **Security**: Vulnerability scanners, penetration testing tools
- **Networking**: TCP/UDP servers, HTTP clients, WebSocket applications
- **Algorithms**: 40+ LeetCode problems with solutions
- **System**: File operations, process management, OS interaction

[View All Examples on GitHub →](https://github.com/sentra-language/sentra/tree/main/examples){: .btn .btn-primary }