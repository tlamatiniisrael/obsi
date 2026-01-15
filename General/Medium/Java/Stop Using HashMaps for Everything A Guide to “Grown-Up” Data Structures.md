
# Stop Using HashMaps for Everything: A Guide to “Grown-Up” Data Structures

## A mostly serious look at the unsung heroes of software engineering that will make your code faster, smarter, and less likely to fall over when the wind blows.

![](https://miro.medium.com/v2/resize:fit:1400/1*phNZb1sjFnhAmSJL14ntfA.png)

Look, I get it. You learned about `ArrayList`, `HashMap`, and maybe `HashSet` in your bootcamp or Algorithms 101 class. They’re warm, they’re fuzzy, they’re comfortable. They are the sweatpants of data structures. You wear them everywhere: to the grocery store, to a wedding, to a high-stakes system design interview.

And for 90% of your CRUD app life, they’re fine. But then one day, your boss asks you to build something that needs to scale beyond a user base of “my mom and her bridge club,” and suddenly your trusty `ArrayList` is choking on its own O(N) searches, and your server’s RAM usage is looking like a hockey stick graph.

It’s time to put on some real pants.

Welcome to the world of specialized data structures — the tools designed by people with PhDs and too much time on their hands to solve specific, hard problems. Let’s meet the crew.

## The “Close Enough is Good Enough” Department (Probabilistic Structures)

These are for when you have so much data that being 100% accurate is just too expensive. You trade a tiny sliver of accuracy for massive performance gains. It’s like asking a friend, “Is there milk in the fridge?” A “probably” is usually good enough to save you a trip to the store.

### The Bloom Filter

**What it is:** A space-efficient, probabilistic set that can tell you if an element “might be present” or is “definitely NOT present.”

**The Magic Trick:** It never lies when it says “no.” Never. If a Bloom filter says your ex’s name isn’t in its database, you can take that to the bank. If it says “yes,” well, there’s a small chance it’s lying.

**Why you need it:** Imagine you’re building a web browser. You have a list of millions of malicious URLs. Before a user visits a page, you need to check this list. Storing all those URLs in a HashSet would eat gigabytes of RAM. A Bloom filter can do it in kilobytes. You check the filter first. If it says “definitely not malicious,” you proceed. Only if it says “maybe malicious” do you bother doing a slow, expensive lookup in your actual database on disk.

**Summary:** The data structure equivalent of a bouncer checking IDs at a club. “You’re not on the list, beat it.” vs. “You look familiar, let me call the manager to double-check.”

### HyperLogLog (HLL)

**What it is:** An algorithm that estimates the number of unique items in a massive dataset (cardinality estimation).

**The Magic Trick:** It can count to a billion unique items with an error rate of less than 2% using only about 1.5 KB of memory. Yes, kilobytes.

**Why you need it:** Your Product Manager wants a dashboard showing the count of unique visitors to your website in real-time. Your first instinct is to throw every IP address into a HashSet and call .size(). Congratulations, you just crashed the server with an OutOfMemoryError. A HashSet of 100 million IPs would need gigabytes of RAM. HyperLogLog does it with almost no memory.

**Summary:** For when your boss asks “How many?” and you know they don’t actually care about the exact number, they just want a big number to put on a PowerPoint slide.

## The “Peacekeepers” (Distributed Consistency)

### CRDTs (Conflict-free Replicated Data Types)

**What it is:** Data structures that can be updated on different computers at the same time, even when offline, and are guaranteed to merge back together into the same final state without any conflicts.

**The Magic Trick:** They use mathematical properties (like commutativity and idempotence, for you math nerds) to ensure that the order of operations doesn’t matter. If I type “A” then “B”, and you type “B” then “A” on another device, we both end up with “AB”.

**Why you need it:** You’re building the next Google Docs or Figma. User A is on a plane editing a document offline. User B is online editing the same paragraph. When User A lands and reconnects, you don’t want a pop-up saying “Merge Conflict! Choose whose work to destroy.” CRDTs handle this gracefully, merging both sets of changes so everyone sees the same final document.

**Summary:** The technological equivalent of two toddlers playing with the same toy and magically not fighting over it. A true miracle of engineering.

## The “Speed Demons & Penny Pinchers” (Performance & Memory)

### Roaring Bitmap

**What it is:** A highly compressed version of a BitSet.

**The Magic Trick:** A regular BitSet is great: it uses one bit to mark if a number is present (1) or absent (0). But if you have the numbers [1, 1000000] in your set, a standard BitSet has to store 999,998 zeros in between them. That’s wasteful. Roaring Bitmaps are smart; they use different storage strategies for dense and sparse regions of data to save tons of space.

**Why you need it:** You’re building a search engine for an e-commerce site. You need to find all users who are “Premium Members” AND “Have made a purchase in the last 30 days” AND “Live in Canada.” Each of these is a massive set of user IDs. Doing set intersections (AND operations) on standard sets is slow. Doing them on Roaring Bitmaps is lightning fast, which is why tools like Elasticsearch and Apache Spark use them under the hood.

**Summary:** Because storing a million zeros just to say “nothing to see here” is dumb, and you should feel bad for doing it.

### The Trie (Prefix Tree)

**What it is:** A tree structure where each node represents a character in a string.

**The Magic Trick:** All words with a common prefix share the same initial path in the tree.

**Why you need it:** Autocomplete. You’re building a search bar. As the user types “app”, you need to instantly suggest “apple,” “application,” “apprehensive,” etc. If you have a list of 100,000 words, iterating through the whole list to find matches starting with “app” is too slow. With a Trie, you just follow the ‘a’ -> ‘p’ -> ‘p’ nodes down the tree, and voilà, all the children nodes are your suggestions. The lookup speed depends only on the length of the word, not how many words are in your dictionary.

**Summary:** It’s the reason your phone can guess you’re trying to type “ducking” when you definitely meant something else.

### The Fenwick Tree (Binary Indexed Tree)

**What it is:** A clever tree structure stored in a simple array that allows you to calculate prefix sums and update values efficiently.

**The Magic Trick:** Let’s say you have an array of numbers and you constantly need to know the sum of the first k elements, but the numbers in the array are also constantly changing. A naive approach is O(N) to calculate the sum. A Fenwick tree makes both the update and the sum calculation O(log N).

**Why you need it:** You’re building a real-time leaderboard for a gaming platform. You need to track total scores for millions of players and frequently update them as games finish. A Fenwick Tree lets you efficiently manage these running totals and quickly query ranges of scores.

**Summary:** For when you need to do basic addition very, very quickly and can’t afford to recount on your fingers every single time a number changes.

## The “Spatial Awareness” Crew (Geometry & Maps)

### Quadtree (2D) / Octree (3D)

**What it is:** A tree that recursively divides a 2D space into four quadrants (or a 3D space into eight octants).

**The Magic Trick:** It turns a spatial search problem (“What’s near me?”) into a tree traversal problem.

**Why you need it:** You’re making a video game. You have a player and 1,000 enemies on a huge map. To check for collisions, the naive approach is to check the player against every single enemy (O(N)) in every frame. Your game will run at 2 frames per second. A Quadtree lets you ask, “Which enemies are in the same map quadrant as the player?” and you only check collisions against those 5 guys.

**Summary:** It’s like cleaning your room by shoving everything into smaller and smaller boxes. But hey, now you know exactly which box your keys are in.

## The “Weird but Wonderful” (Niche Utilities)

### Disjoint Set Union (DSU) or Union-Find

**What it is:** A data structure that keeps track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets.

**The Magic Trick:** It has two primary operations: find (which set does this element belong to?) and union (merge two sets together). With some clever optimizations (path compression and union by rank), these operations are almost constant time, practically O(1).

**Why you need it:** You have a network of computers and you want to know if Computer A can communicate with Computer B (i.e., are they in the same connected component of the network graph?). As links are added between computers, you use union to merge their sets. To check connectivity, you use find on both and see if they return the same representative ID. It’s blazingly fast for dynamic connectivity problems.

**Summary:** Perfect for managing high school cliques. It quickly tells you who is in the “cool kids” group and who belongs to the “band geeks,” and can instantly merge groups when a strategic alliance is formed.

## **Conclusion**

So there you have it. A whole toolbox of shiny new hammers for you to go and smash things with. The next time you instinctively reach for a HashMap to solve a complex distributed systems problem, pause for a moment. Ask yourself: “Is there a weird, named-after-a-dead-mathematician data structure that would do this better?”

The answer is almost certainly yes. Now go forth and over-engineer something. Your job security depends on it.

## A message from our Founder

**Hey,** [**Sunil**](https://linkedin.com/in/sunilsandhu) **here.** I wanted to take a moment to thank you for reading until the end and for being a part of this community.

Did you know that our team run these publications as a volunteer effort to over 3.5m monthly readers? **We don’t receive any funding, we do this to support the community. ❤️**

If you want to show some love, please take a moment to **follow me on** [**LinkedIn**](https://linkedin.com/in/sunilsandhu)**,** [**TikTok**](https://tiktok.com/@messyfounder), [**Instagram**](https://instagram.com/sunilsandhu). You can also subscribe to our [**weekly newsletter**](https://newsletter.plainenglish.io/).

And before you go, don’t forget to **clap** and **follow** the writer️!