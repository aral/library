# Substance Talk

Substance Talk is a protocol for exchanging data between multiple parties using the network. A party can be a web-browser, a web-server or any network program that can handle the talk protocol. The communication is done by messages (expressed as JSON) and works in both directions. You can use any technology to implement a program that participates in a Substance talk.

The idea is simple: Users should no longer worry about the implementation details of networking (such as HTTP headers etc.). People talk in messages and computers should too. Substance Talk is intended to be used with stateful connections, made possible by TCP sockets or websockets. It's a good choice for designing realtime applications and distributed systems. 


## Talk Messages

A message consists of three properties at least:

- `to` - Unique identifier of the recipient (optional)
- `command` telling the other party what you want. The command is used for routing within the recipient


All other properties can be used to add arbitrary data to your message.


## Maintaining State

Maintaining state has always been one of the most challening things in computer science. So if you're going to use Websockets you should be aware that you need to spend some time with modelling your state data. Let me say this is crucial if you want do design a reliable system. No framework will do that job for you.

# Usage

The Talk protocol is used by Substance to synchronize multiple clients that are editing a document in realtime. 

In our case each document:create or document:open command modifies the state of a client. In other words the client gets assigned a document, and once that is done messages are exchanged between all clients that also have that document open.


## Example communication


Client1 -> Server: Create an empty document

```js
{
  command: "document:create",
  id: "hello-world"
}
```

Server -> Client1: Done. With success.

```js
{
  status: "success",
  session-id: 87123
}
```

Client2 -> Server: Open document `hello-world`

```js
{
  command: "document:get",
  id: "hello-world"
}
```

Server -> Client2: Done. There's two active users, including you.

```js
{
  status: "success"
  document: {...},
  active-users: ["client1", "client2"]
}
```

Client1 -> Server: Create new task


```js
{
  command: "node:create",
  type: "/type/task",
  data: {
    name: "A new task"
  }
}
```

Server -> Client 1: Confirmed.


Server -> Client 2:

```js
{
  command: "node:create",
  type: "/type/task",
  data: {
    name: "A new task"
  }
}
```
