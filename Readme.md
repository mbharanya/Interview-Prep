# Systems Design

These will be my own attempts at building systems from the requirements from the course at https://www.educative.io/courses/grokking-the-system-design-interview

## Pastebin
> Pastebin like services enable users to store plain text or images over the network (typically the Internet) and generate unique URLs to access the uploaded data. Such services are also used to share data over the network quickly, as users would just need to pass the URL to let other users see it.

### Requirements
> Our Pastebin service should meet the following requirements:
>
> Functional Requirements:
> 
> Users should be able to upload or “paste” their data and get a unique URL to access it.
> Users will only be able to upload text.
> Data and links will expire after a specific timespan automatically; users should also be able to specify expiration time.
> Users should optionally be able to pick a custom alias for their paste.  
> Non-Functional Requirements:
> 
> - The system should be highly reliable, any data uploaded should not be lost.
> - The system should be highly available. This is required because if our service is down, users will not be able to access their Pastes.
> - Users should be able to access their Pastes in real-time with minimum latency.
> - Paste links should not be guessable (not predictable).
> Extended Requirements:
> 
> - Analytics, e.g., how many times a paste was accessed?
> - Our service should also be accessible through REST APIs by other services.


### Design

Questions:
- What's the limit of the expiration, what should we set the default to? 
    - Guess: Limit 5y; default 6 months
- What's the size limit of the text?
    - 10MB sounds sane
- Alias should be limited to certain amount of characters. URL key should be based on amount of pasted possible.
Using base64 we could have a key of size 64^6 = 68719476736 possibilities. The user could also choose 6 characters.
- When expiration period is reached, should we actually delete the data, or give them some grace-period?
- Can users change the custom url after it's been created?
    - Offer: don't allow it, but allow to delete it, users can recreate it later

API:
```scala
// can also return 400 Bad Request, if customName is already taken
submitPaste(pasteContent: String(10MB), userId: Long, expirationDate: Option[Date], customName: Option[String]): ResultUrl

// deltaData can use diff format used by git etc (contains +/- and line numbers)
submitDelta(urlKey: String, deltaData: String): ResultUrl
```

Scale:  
This service is Read-heavy. Let's consider a ration of 100:1 read to write. 

Let's also assume 
```
1000 writes per hour = 100'000 reads per hour
-> 0.27 wps = 277.77 rps

Max read bandwidth: 278 * 10MB = 2780 MB/s
Max write bandwidth: 0.27 * 10MB = 2.7 MB/s

```

Users will want to edit their data, depending on scale we could only send deltas to the server, this would reduce the max write bandwidth, but also increase the chance of getting bad data. The client could periodically send the full content as well.


Storage:
I'd recommend a NoSQL database, the data does not have strong relational data.

Database Design
Sharding
