Instructor: [00:01] After some custom development, 
[00:03] we want to make another change 
[00:04] to our workshop. We plan to add 
[00:06] a section to the detail page, 
[00:08] previewing recommended adapters 
[00:10] and Access files for each laptop.



[00:13] To do so, we can extend our 
[00:15] sample query by a field 
[00:17] recommended products and ask for 
[00:19] the names and image URLs. In the 
[00:22] result, we would receive a list 
[00:24] of recommended products. For 
[00:27] example, a USB to HDMI adapter, 
[00:36] as well as a USB to SD card 
[00:38] adapter.



[00:39] This only works well 
[00:41] as long as the list never grows 
[00:43] beyond the reasonable size to 
[00:44] fetching one goal. In our case 
[00:46] though, the list of Access files 
[00:48] might include hundreds of 
[00:50] different products. 



[00:51] What to 
[00:52] do in such a case? Pagination to 
[00:54] the rescue. We add limit 
[00:56] arguments to limit amount of 
[00:58] recommended products, fetching 
[01:00] them on request. With the page 
[01:01] argument, we can fetch one 
[01:03] specific page at a time.



[01:06] There are several issues, though, 
[01:07] with this approach. One of them 
[01:09] is that, we can't tell if 
[01:11] there's a previous or next page. 



[01:14] Before explaining all the other 
[01:16] issues, I want to briefly show 
[01:17] you the established best 
[01:19] practice, solving all these 
[01:21] issues at once, the relay cursor 
[01:24] connections specification, or 
[01:26] short, connections spec.



[01:28] It's 
[01:28] a specification that defines a 
[01:30] standardized way for exposing 
[01:32] connections in GraphQL. While it 
[01:34] originated in relay, it doesn't 
[01:37] require us to use relay, but 
[01:39] turned out to be a really good 
[01:41] idea, and so became a best 
[01:43] practice.



[01:45] Let me illustrate 
[01:45] it by creating an example query 
[01:48] to retrieve recommended products. 
[01:51] We would use the argument, first, 
[01:53] to limit the result and provide 
[01:55] a cursor -- basically the ID of 
[01:58] the last item of the previous 
[01:59] page -- to the argument, after. 



[02:02] If we want to fetch the previous 
[02:04] page, we can provide last five 
[02:08] and before, with a cursor.



[02:10] Now, instead of directly asking 
[02:12] for the name and image, we get 
[02:14] back edges and pageInfo. Edges 
[02:18] return a list of nodes. In there, 
[02:21] we can finally query for the 
[02:23] name and the image. pageInfo 
[02:27] contains hasNextPage, 
[02:29] hasPreviousPage, startCursor, 
[02:32] and then endCursor.



[02:35] While 
[02:35] it's not obvious yet, let me 
[02:37] tell you that this will solve 
[02:39] all the problems with 
[02:40] connections you can possibly 
[02:42] imagine. Maybe not all of them, 
[02:45] if you're very creative, but 
[02:46] most of them.



[02:48] All right. 
[02:49] Before we dive more into the 
[02:51] explanations of how and why, 
[02:54] let's talk about the elephant in 
[02:55] the room. It looks ugly, and 
[02:58] it's way more bloated than feels 
[03:01] necessary. 



[03:02] Nevertheless, in 
[03:04] the next couple minutes, I 
[03:05] hopefully will change your mind, 
[03:07] and you'll consider using the 
[03:08] connections spec a good choice.



[03:11] How do we get there? Let's take 
[03:13] a step back and start from our 
[03:15] initial pagination design and 
[03:17] let one issue with a suitable 
[03:19] solution after another. 



[03:21] First, we need to retrieve the 
[03:23] information if there's a 
[03:25] previous and next page. To do so, 
[03:28] we can add another field, 
[03:30] recommend the product's page 
[03:31] info, pull out the same 
[03:34] arguments, and create a has next 
[03:37] page, as well as has previous 
[03:38] page. Not particularly exciting 
[03:41] to duplicate the arguments.



[03:43] We can refactor it and nest both 
[03:45] inside one field. Way better. 



[03:54] All right, so next up. Our CLS 
[03:56] team recommends us to show how 
[03:59] often [inaudible] SSR has been 
[04:00] brought to get out with the 
[04:02] current product. Let's add to 
[04:04] field bought to get a percentage 
[04:07] to our query.



[04:08] At first sight, 
[04:10] this looks like it's going to be 
[04:11] completely fine. There's one 
[04:13] issue though that will only show 
[04:15] itself once we write down the 
[04:17] type definitions. So let's do 
[04:19] that.



[04:21] We add the field 
[04:21] recommended products to product, 
[04:25] and add the type recommended 
[04:27] product connection. In there, we 
[04:31] add the field products, 
[04:32] returning a list of products. 
[04:34] Then, we add bought to get a 
[04:36] percentage to product. 



[04:39] Exactly this is where we have a 
[04:41] problem. Bought to get a 
[04:42] percentage only makes sense in 
[04:45] relation to another product.



[04:47] If you query for a single 
[04:48] product, this field doesn't make 
[04:50] sense at all. To tackle this 
[04:52] issue, we can create a new type, 
[04:55] recommended product with the 
[04:57] field bought to get a percentage, 
[04:59] and change our connection time.



[05:13] One thing that bothers me, 
[05:15] though, is that there's a lot of 
[05:17] duplication. This isn't 
[05:19] necessary. Instead of spreading 
[05:21] out all the fields, we can 
[05:23] simply refer to the product. 



[05:27] So far, so good. What I have to 
[05:29] add here is the outcome. So far, 
[05:31] is definitely more complex than 
[05:33] referring to a list of product 
[05:35] with the field recommended 
[05:36] products in the connection type. 
[05:39] On the other hand, it's a 
[05:40] beautiful solution, allowing us 
[05:42] to attach meet information.



[05:45] Now that our types are in a good 
[05:47] state, we go back to our sample 
[05:49] query and check how this type 
[05:51] changes affect it. We change 
[05:53] products to recommend products, 
[05:56] and this name and image inside 
[05:58] product.



[06:03] My next concern is 
[06:05] the page argument. To explain 
[06:07] why using a page index isn't a 
[06:09] good idea, I best walk you 
[06:11] through a couple of data 
[06:12] fetching scenarios.



[06:14] We can 
[06:14] start with a page index scenario 
[06:16] that comes with [inaudible] 
[06:17] issues. On the left side, we see 
[06:20] a list of items stored in the 
[06:21] data base. If decline request 
[06:24] the first page with a limit of 
[06:26] five, the server will return the 
[06:28] first five items. Then, decline 
[06:31] can ask for the second page and 
[06:33] we'll receive the items 6 until 
[06:35] 10. So far, so good.



[06:38] Let's 
[06:38] start over. The client ask for 
[06:41] the first page and receives the 
[06:42] first five items. Before asking 
[06:45] for the second page, something 
[06:47] else is happening. Another user 
[06:50] removes the items three and four 
[06:52] from the list. This is obviously 
[06:55] affecting the pages.



[07:00] Now, our 
[07:00] client requests the second page 
[07:04] and receives items 8 until 12. 
[07:09] Our client is missing item six 
[07:11] and seven. No warning, no 
[07:13] indication that the client 
[07:15] missed these entries. Depending 
[07:18] on your context, this can be 
[07:19] neglectable, but in some 
[07:21] environments, missing a message 
[07:24] while on entry, can lead to 
[07:26] horrible outcomes.



[07:27] Can we 
[07:27] prevent it? Of course. Cursors 
[07:30] to the rescue. Not these cursors, 
[07:33] but rather the concept of 
[07:34] providing an ID as a reference 
[07:36] for position in the list. How 
[07:39] does this work? Let's go for the 
[07:41] last scenario once again, but 
[07:43] now with cursors.



[07:44] The client 
[07:45] requests the first five items 
[07:47] and leaving out the cursor, 
[07:48] basically means giving the first 
[07:50] page. Then, items three and four 
[07:53] are removed. After that, the 
[07:55] client requests the first five 
[07:57] items after the cursor ID 5, 
[08:01] because this is the last ID our 
[08:02] client was aware of.



[08:04] As you 
[08:05] can see, the item 6 to 10 are 
[08:07] returned and no items are 
[08:09] missing. Fantastic. That said, 
[08:13] with our real time updates, 
[08:14] users might still miss if 
[08:16] entries have been deleted. Like 
[08:18] in our case, item three and four 
[08:20] are still on the list of the 
[08:21] client. 



[08:22] Nevertheless, at 
[08:24] least the client won't miss a 
[08:25] message, which is definitely 
[08:27] better than missing them.



[08:29] What does this mean for our 
[08:30] query? We replace "page" with "
[08:32] after" and passing the ID of the 
[08:35] last item was called mCursor. 
[08:38] After convenience, we can add 
[08:40] the field mCursor to our page 
[08:42] info.



[08:44] This allows us to fetch 
[08:45] the next page, but how can we 
[08:47] fetch the previous page using 
[08:49] cursors? Simply by replacing the 
[08:52] argument "after" with "before." 
[08:54] In our implementation, we can 
[08:55] make sure the once "before" is 
[08:57] provided, we retrieve to 
[08:59] previous items.



[09:00] Again, out of 
[09:01] convenience, we can add the 
[09:03] field "start cursor" to our page 
[09:05] info.



[09:08] Now, we are at a point 
[09:10] where it's really interesting to 
[09:11] compare our current query with 
[09:13] the connection spec example we 
[09:15] wrote before.



[09:17] The structure 
[09:18] is identical and the only 
[09:20] difference is the naming of 
[09:21] certain fields. This makes a lot 
[09:24] of sense, because if we start to 
[09:27] generalize these names and 
[09:29] replace recommended products 
[09:30] with edges, product with node 
[09:33] and limit with last, we get the 
[09:35] exact same result. Fascinating.



[09:39] By applying several common 
[09:41] feature requests, we ended up 
[09:44] with the connection spec.



[09:48] I 
[09:48] hope with this little exercise, 
[09:50] I could convince you that while 
[09:52] the connection spec comes with 
[09:53] some complexity, it's a really 
[09:55] good idea. Make sure your scheme 
[09:58] is ready for changing 
[09:59] requirements. Pretty cool.



[10:02] Before we conclude the lesson, 
[10:03] let us implement the type 
[10:05] definitions so you have seen it 
[10:07] at least once. We changed 
[10:09] arguments to accept first 
[10:11] integer, after ID, last integer, 
[10:14] and before ID.



[10:17] Then we 
[10:17] changed products to edges in the 
[10:20] recommended product connection, 
[10:21] and product to node in the 
[10:24] recommended product. Since the 
[10:25] recommended product is part of a 
[10:27] connection, it also makes sense 
[10:29] to post fix it with edge.



[10:32] At 
[10:32] the final step, we add the type 
[10:35] page info containing has next 
[10:37] page, has previous page, start 
[10:39] cursor, and end cursor. Then add 
[10:42] it to our connection. We copy 
[10:45] our example query and verify 
[10:47] that it works. 



[10:52] Great. To 
[10:57] fetch the next page, we only 
[10:59] need to replace the arguments 
[11:01] and query for first five and 
[11:03] after CDE. Works like a charm.


