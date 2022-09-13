---
title: "Identify your OBIEE users by setting Client ID in Oracle connection"
date: "2010-01-26"
categories: 
  - "obiee"
  - "oracle"
  - "support"
---

You get a call from your friendly DBA. He says the production database is up the spout, and it's "that bee eye thingumy causing it". What do you do now? All you've got to go on is a program name in the Oracle session tables of "nqsserver@MYSERVER (TNS V1-V3)" and the SQL the DBA sent you that if you're lucky will look as presentable as this: ![](/images/rnm1978/2010-01-25_145658.png "2010-01-25_145658") The username against the SQL is the generic User ID that you had created for connections to the database from OBIEE.

So you turn to Usage Tracking and discover that when that particular SQL ran there were twenty users all running reports. And not only that, but the SQL that's recorded is the Logical SQL, not the physical SQL.

So how do you identify the originating report that spawned the SQL that broke the database that upset the DBA that phoned you? ...

With a large hat-tip to [Mark Rittman](http://www.rittmanmead.com), here's one thing you can do to help matters. Within the Connection Pool object in the RPD you can add statements to execute at the beginning of each connection. In this case, we can set the Client ID for the user running the request. \[sourcecode\] call dbms\_session.set\_identifier('VALUEOF(NQ\_SESSION.USER)') \[/sourcecode\]

![](/images/rnm1978/2010-01-25_150200.png "2010-01-25_150200") ![](/images/rnm1978/2010-01-25_150232.png "2010-01-25_150232") ![](/images/rnm1978/2010-01-25_150305.png "2010-01-25_150305")![](/images/rnm1978/2010-01-25_150721.png "2010-01-25_150721")

Now when you look at the queries from OBIEE running on the database you'll see the Client ID column is populated : ![](/images/rnm1978/2010-01-25_155437.png "2010-01-25_155437") ![](/images/rnm1978/2010-01-26_090730.png "2010-01-26_090730")![](/images/rnm1978/2010-01-26_090712.png "2010-01-26_090712")![](/images/rnm1978/2010-01-26_090647.png "2010-01-26_090647") This helps you trace SQL from the database back to the originating user.

My only question about this is with regards to connection pooling. [The documentation](http://download.oracle.com/docs/cd/E10415_01/doc/bi.1013/b31770.pdf) states that the Execute on Connect is run "...each time a connection is made to the database." - but if connection pooling is enabled then by definition the connection is re-used so the client ID will only be set for the first user into the connection pool. **However** this doesn't seem to be the case as on the database I see different Client IDs against the same session.
