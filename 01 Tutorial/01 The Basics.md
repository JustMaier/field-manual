# Tutorial Part 1 - Laying the Foundation

> The basics of OrbitDB include _installing IPFS and OrbitDB_, _creating a new IPFS node_, _creating databases_, and understanding  _data types_, 

## Chapter 1: The Basics


Finally, since all of these activities can happen offline and locally, without being connected to any peers at all, these
steps will all take place offline.

### Instantiating OrbitDB

Require OrbitDB and IPFS in your program and create the instances:

#### Installation in Node.js

To use these modules in node.js, first create your project directory and use npm to install
`orbitdb` and its dependency `ipfs`

From the command line:
```bash
$ npm install orbitdb ipfs
```

Then, in a script called `server.js`, require the modules:

```javascript
// server.js

const Ipfs = require('ipfs')
const OrbitDB = require('orbit-db')
```

#### Installation in the Browser

There are a few different places you can get browser packages for ipfs and orbitdb. These are detailed in Part 3 of this book. For the purposes of this tutorial, we recommend using unpkg for both.

Simply include these at the top of your `index.html` file:

```html
<script src="https://unpkg.com/ipfs/dist/index.min.js"></script>
<script src="https://www.unpkg.com/orbit-db/src/OrbitDB.js"></script>
```

### Creating IPFS and OrbitDB instances

Let's start with the following code. We'll try to keep the code in bite sized chunks. We are also going to start "offline",
purposefully not connecting to any other peers until we absolutely need to.

OrbitDB's syntax is almost always identical between the browser and in node.js, so this code will work in both places:

```javascript
let orbitdb

let ipfs = new Ipfs({
  preload: { enabled: false },
  repo: "./ipfs",
  EXPERIMENTAL: { pubsub: true },
  config: {
    Bootstrap: [],
    Addresses: { Swarm: [] }
  }
});

ipfs.on("error", (e) => { throw new Error(e) })
ipfs.on("ready", async () => {
  orbitdb = await OrbitDB.createInstance(ipfs)
  console.log(orbitdb.id)
})
```

You see the output, something called a "multihash", like `QmPSicLtjhsVifwJftnxncFs4EwYTBEjKUzWweh1nAA87B`. For now, just know that this is the identifier of your node. Multihashes are explained in more detail in the **Part 2: Peer-to-Peer**

#### What just happened?

Starting with the `new Ipfs` line, your code creates a new IPFS node. Note the default settings:

* `preload: { enabled: false }` disables the use of so-called "pre-load" IPFS nodes. These nodes exist to help load balance the global network and prevent DDoS. However, these nodes can go down and cause errors. Since we are only working offline for now., we include this line to disable them.
* `repo: './ipfs'` designates the path of the repo in node.js only. In the browser, you can actually remove this line. The default setting is a folder called `.jsipfs` in your home directory. You will see why we choose this acute location for the folder later.
* `XPERIMENTAL: { pubsub: true }` enables IPFS pubsub, which is a method of communicating between nodes and is required for OrbitDB usage, despite whether or not we are connected to other peers.
* `config: { Bootstrap: [], Addresses: { Swarm: [] }}` sets both our bootstrap peers list (peers that are loaded on instantiation) and swarm peers list (peers that can connect and disconnect at any time to empty. We will populate these later.
* `ipfs.on("error", (e) => { throw new Error(e) })` implements extremely basic error handling for if something happens during node creation
* `ipfs.on("ready", (e) => { orbitdb = new OrbitDB(ipfs) })` instantiates OrbitDB on top of the IPFS node, when it is ready.

By running the code above, you have created a new IPFS node that works locally and is not connected to any peers.
You have also loaded a new orbitdb object into memory, ready to create databases and manage data.

*You are now ready to use OrbitDB!*

* Resolves #[367](https://github.com/orbitdb/orbit-db/issues/367)

##### What else happened in node.js?

When you ran the code in node.js, you created two folders in your project structure: `'orbitdb/` and `ipfs/`. 

```bash
$ # slashes added to ls output for effect
$ ls orbitdb/
QmNrPunxswb2Chmv295GeCvK9FDusWaTr1ZrYhvWV9AtGM/

$ ls ipfs/
blocks/  config  datastore/  datastore_spec  keys/  version
```

Focusing your attention on the IPFS folder, you will see that the subfolder has the same ID as orbitdb. This is purposeful, as this initial folder contains metadata that OrbitDB will need to operate. For now let's not go into detail

The `ipfs/` folder contains all of your IPFS data. Explaining this in depth is outside of the scope of this tutorial, and the curious can find out more [here](#). 

##### What else happened in the browser?

In the browser IPFS content is handled inside of IndexedDB, a persistent storage mechanism for browsers

![alt browser whatever](../images/ipfs_browser.png)

Note since you have not explicitly defined a database in the broser, no IndexedDB databases have been created for OrbitDB yet.

**Caution!** iOS and Android have been known to purge IndexedDB if storage space needs to be created inside of your phone. We recommend creating robust backup mechanisms at the application layer.

## Creating a Database

Now you will create a local database that *only you8 can read.

Remember the code snippet from above, starting and ending with:

```javascript
let orbitdb

/* ... */

ipfs.on("ready", () => {
  orbitdb = new OrbitDB(ipfs)
  console.log(orbitdb.id)
})
```

Expand that to the following:

```javascript
let orbitdb, pieces

/* ... */

node.on("ready", async () => {
  orbitdb = await OrbitDB.createInstance(node)

  const options = {
    indexBy: "hash",
    accessController: {
      write: [orbitdb.identity.publicKey]
    }
  }
  
  pieces = await orbitdb.docstore('pieces', options)
  console.log(pieces)
})
```

Run this code and you will see on object containing information about the new database you just created

See for more https://github.com/orbitdb/orbit-db/blob/525978e0a916a8b027e9ea73d8736acb2f0bc6b4/src/OrbitDB.js#L106

### What Just Happened?

* Resolves #[366](https://github.com/orbitdb/orbit-db/issues/366)
* Resolves #[502](https://github.com/orbitdb/orbit-db/issues/502)

### Data Types

* Resolves #[481](https://github.com/orbitdb/orbit-db/issues/481)
* Resolves #[480](https://github.com/orbitdb/orbit-db/issues/480)
* Resolves #[400](https://github.com/orbitdb/orbit-db/issues/400)
* Resolves #[318](https://github.com/orbitdb/orbit-db/issues/318)
* Resolves #[503](https://github.com/orbitdb/orbit-db/issues/503)