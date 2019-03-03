## Chapter 5: Peer-to-Peer Part 2 (OrbitDB)

> TODO: Description

<div>
  <h3>Table of Contents</h3>

Please complete [Chapter 4 - Peer to Peer](./04_P2P_Part_1.md) first.
 
- [Connecting to another peer's database](#connecting-to-another-peers-database)
- [Key takeaways](#key-takeaways)

</div>

### Connecting to another peer's database

To share data between peers, you will need to know their OrbitDB address. Unforutately, simply connecting to a peer is not enough, since there's not an simple way to obtain databases address from a simply IPFS peer-to-peer connection. To remedy this, you'll write your OrbitDB address to IPFS. Then, you'll share the hash to peers you want to connect to. 

It will be helpful to ensure you are connected to the peer first, via the steps in the previous chapter.

Create a function called `processMessage` and update `connectToOrbitDb` inside the `NewPiecePlease` class:

```diff
     <body>
         <nav>
-            <a href="#">Link 1</a>
-            <a href="#">Link 2</a>
+            <h1>New Piece Please</h1>
+
+            <form id="getRandomByInstrumentForm">
+                <slot name="instrumentList"></slot>
+                <input type="submit" value="New Piece, Please!" />
+            </form>
+
+
+            <h3>User Profile</h3>
+            <dl id="profileList"></dl>
+
+
+            <form id="getByHashForm">
+                <h3>Get score by IPFS hash</h3>
+                <label>Get from hash: <input type="input" name="hash" /></label>
+                <input type="submit" value="New Piece, Please!" />
+            </form>
         </nav>


+ async connectToOrbitDb(multiaddr) {
  try {
    var peerDb = await this.orbitdb.keyvalue(multiaddr)
    await peerDb.load()

    var peers = Object.assign({}, this.userDb.get("peers"))
    peers[peerDb.id] = peerDb._index._index
    console.log(peers)
  } catch (e) {
    throw (e)
  }
}

processMessage(msg) {
  const parsedMsg = JSON.parse(msg.data.toString())
  Object.keys(parsedMsg).forEach(async (key) => {
    switch(key) {
      case "userDb":
        this.connectToOrbitDb(parsedMsg.userDb)
        const peerDb = await this.orbitdb.keyvalue(parsedMsg.userDb)
        this.sendMessage(peerDb.get("nodeId"), {
          userDb: this.userDb.id
        })
        break;
      default:
        break;
    }
  })
}
```

Inside your application code, you can use it like so:

```javascript
```

### Replication vs. Duplication

### Key takeaways

Continue to [Chapter 6](./06_Identity_Permissions.md) to learn about how you can vastly extend the identity and access control capabilities of OrbitDB