Instructor: [00:00] In this lesson, we want to expose the image. Based on the wire frame, we would only need the URL. Let's add the field image to the query and then expect the following result. Pretty straight forward.

[00:16] Also, in the schema definition, we simply add the field image of type string to the type product. Then we can test it by adding image to the query. Works like a charm. We can ship it to production.

[00:33] What happens next? After evaluating some customer feedback, it becomes clear that an image description for accessibility and additional thumbnail URL for a smaller version of the images needed. Nothing easier than that.

[00:47] We can change our image to have its own fields, URL, description, thumbnail URL, accepting width and height arguments. Our result will change to something like this.

[01:09] Shipping this change, though, wouldn't be a good idea. Why? Because it breaks the expectations of all our existing clients. If we switch to the type definitions, you can see that the field, image, is currently a string. In case we shipped it like this, our clients probably would have the same expectation. This, most likely, would result in errors occurring on the clients. We made a breaking change.

[01:36] In order to avoid a breaking change, we have to do something different. One way would be to spread out the image-related fields directly into product, and prefix them with image.

[01:49] While this definitely works, this isn't great to work with in the long run. For example, if we use image for multiple entities, every change on image would have an impact on these entities, like it does currently on our product.

[02:04] Another approach is going for different name than image. For example, image_opchecked. The name image_opchecked, though, is not what we have been aiming for, but it's not a breaking change and we have our desired structure.

[02:24] Now, the million-dollar question. Could we have avoided this? I strongly believe so. Think about, we would have named the field image_url in the beginning. Adding image later on is not an issue. Let's verify this and check how this would look like in our type definitions.

[02:44] We can add the field image and return a new type image. Then we add our new type image including the desired fields. Once saved, we can grab our existing query and see if it works as expected. Voila, works like a charm.

[03:10] The important takeaway here is that naming fields can have quite an impact on future schema changes. Being very explicit early on will make things easier later on. Let me give you a couple more examples. In case you want to fetch the amount of tables in a restaurant, it's better to name it tablesCount or tablesAmount instead of just tables.

[03:33] Why so? Because if table becomes its own type having multiple fields, tablesCount can stick around and we can add the field tables listing the actual tables. Same goes for likes. If our user interface is only showing the number of likes for a post, it's better to name it likesCount or likesAmount.

[03:55] This allows us to later on provide a field likes containing actual like objects with references to the like office. Another example would be URLs. When we link to repository, it's best to name it repositoryURL. While it's good to be explicit, someone also obviously can overdo it. For example, appending the type to the name usually is not a good idea.

[04:23] Let me illustrate this by likesCount integer. We can enable Boolean. Similar to this, the same rule applies to obvious properties like text and description text. Remember, being explicit is a good thing but don't overdo it.
