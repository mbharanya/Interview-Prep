# Systems Design

These will be my own attempts at building systems from the requirements from the course at https://www.educative.io/courses/grokking-the-system-design-interview

## Pastebin
> Pastebin like services enable users to store plain text or images over the network (typically the Internet) and generate > unique URLs to access the uploaded data. Such services are also used to share data over the network quickly, as users > would just need to pass the URL to let other users see it.

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
- 