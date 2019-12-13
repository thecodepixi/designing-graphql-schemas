Instructor: [0:00] We already have a mutation updating a connection, add products to cart. Next up, we want to allow users to remove products. No problem. We can add the mutation, remove products from cart.

[0:17] Now we have two mutations to manipulate the products in the cart connection. Should we implement an update products in cart mutation instead? In almost all situations, the answer should be no. Let me elaborate why.

[0:33] Think about the situation where the user has two tabs of the web shop open. In Tab A, she adds the product with the ID ABC. Later on, in Tab B, she adds the product with the ID BCD. In case we would use an update products in cart mutation, the first mutation in Tab A would update the list to include ABC.

[0:55] Without any real-time updates in Tab B, though, the current state is still referring to an empty list of products. Adding the product BCD will also mean the list only includes BCD. On the server, this would be the end result. Let's compare it to add products to cart.

[1:17] Since this mutation doesn't rely on the current state, the clients can be out of date, but the end result on the server will still be correct and include ABC as well as BCD. That's exactly why an add products to cart and remove products from cart is superior to an update products in cart.

[1:37] In some edge cases, though, it's required to reset or update the full list. In such a case, not all hope is lost. You can implement a validation strategy where the client has to send information of the latest state in form of the full list or a content hash.

[1:54] The server can validate if the input is correct and, depending on the use case, abort the action or reply with information about the lost items.
