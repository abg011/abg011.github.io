---
layout: post
title: "Go Concurrency Explained"
date: 2025-11-13
---

# Go Concurrency Explained 😎  
### Idiomatic Code, Shared Channels aur Done Signals ka Full Scene  

---

## 🧠 Goroutine kya hota hai?

Simple bhai —  
> Goroutine ek lightweight thread hai jo Go runtime khud manage karta hai.  

OS thread nahi hota — Go ka runtime apne M:N scheduler se M goroutines ko N OS threads pe efficiently map karta hai (M >> N, aur N typically CPU cores ke barabar hota hai).  

Aur **channel**? Ek safe line hai jahan se goroutines baat karte hain — bina lock ke.  

```go
c := make(chan int)
go func() { c <- 42 }()
fmt.Println(<-c)
```

Bas — ek ne bheja, doosre ne liya. No tension.

---

## ⚙️ “Idiomatic Go” ka matlab kya hota hai?

“Idiomatic” matlab **Go ka natural tareeka** likhne ka.  
Aise likho jaise Go khud keh raha ho — “haan bhai, ab sahi likha.”

| Non-idiomatic | Idiomatic |
|---------------|------------|
| Flag se signal dena | Channel se signal dena |
| Manual locks | Use channels for sync |
| Receiver closes channel | Sender closes channel |
| Random if-else | Use `select` for control |

Idiomatic code = Simple + Safe + Predictable ✅

---

## 🚦 Unbuffered Channel ka Scene

```go
c := make(chan int)

go func() {
	fmt.Println("sending...")
	c <- 99
	fmt.Println("sent!")
}()

time.Sleep(1 * time.Second)
fmt.Println(<-c)
```

**Output:**
```
sending...
sent!
99
```

Yaani sender tab tak rukta hai jab tak receiver ready nahi — perfect synchronization point 💪

---

## 💣 Shared Channel Fatega Agar Galat Close Kiya

Agar 2-3 goroutine ek hi channel pe likh rahe hain aur koi ek ne close kar diya —  
💥 Boom! `panic: send on closed channel`

---

## ✅ Solution — Ek Banda Close Karega

```go
var wg sync.WaitGroup
c := make(chan int)

for i := 0; i < 3; i++ {
	wg.Add(1)
	go func(id int) {
		defer wg.Done()
		c <- id
	}(i)
}

go func() {
	wg.Wait()
	close(c) // ek hi banda close karega
}()

for v := range c {
	fmt.Println(v)
}
```

Idhar sab chill hai — koi panic nahi, koi leak nahi 🔥

---

## 🧘 Early Stop with `done`

Jab pehla hi mismatch mil gaya, toh baaki goroutines ko politely bol do — "bhai ruk jao."

```go
done := make(chan struct{})

select {
case ch <- value:
case <-done:
	return // respectful exit
}
```

`close(done)` sab goroutines ko ek saath unblock kar deta hai — ekdum clean exit.

---

## ⚡ Flow Samjho (Diagram)

```mermaid
flowchart LR
    A[Main] -->|go| B[Walk Goroutine 1]
    A -->|go| C[Walk Goroutine 2]
    B -->|values| D[(Channel)]
    C -->|values| D
    A -->|compare| D
    A -->|mismatch| E[close(done)]
    B -->|<-done| F[exit]
    C -->|<-done| G[exit]
```

---

## 🔑 Bhai’s Takeaways

| Concept | Simple Bhai Style |
|----------|------------------|
| Idiomatic Go | Go ka natural tareeka |
| Unbuffered channel | Send/receive sync point |
| Shared channel | Ek hi close kare |
| Early stop | `done` signal se |
| Goroutine leak | Jab koi hamesha block rahe |
| WaitGroup | Sync karne ka sabse safe jugad |

---

### 🎯 Closing Line

Go ka concurrency model simple hai —  
bas discipline aur idioms follow kar lo.  
Channel aur goroutine sahi use kar liye toh likh lega tu bhi **production-grade concurrent code** 🚀
