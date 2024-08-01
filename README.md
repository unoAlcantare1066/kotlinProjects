# Go pub/sub library

This library provides a `PubSubChannel` that allows several subscribers to receive the same message asynchronously generated 
by a publisher.

Both publisher or subscribers could end the communication (although only the publisher will close the channel). 
Subscribers calling `Unsubscribe()` on the `Subscription` returned by the `PubSubChannel` will notify the publisher
that it wants to end communication. The publisher will close the channel eventually when a new message is sent or when
itself call `Close()` on `PubSubChannel`. (_TODO: maybe schedule a routine to close periodically channels_)

Example of use:
```go
package main

import (
    "fmt"
    "github.com/kortatu/gopubsub"
)
func main() {

    //...
    pubSub := gopubsub.New()


    subscription1 := pubSub.Subscribe()
    subscription2 := pubSub.Subscribe()
    
    // each subscription
    go func(subscription gopubsub.Subscription) {
        msg := <- subscription.ReceiveChannel()
        fmt.Println("Message received", msg, "subscription id", subscription.ID())
        subscription.Unsubscribe()
    }(subscription1)
    // ...

    pubSub.Publish(struct {}{})
    //...
    // Close all subscriptions not closed
    pubSub.Close()
}
```
