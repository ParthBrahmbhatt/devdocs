---
group: graphql
title: quote endpoint
---

A quote represents the contents of a customer's shopping cart. It is responsible for performing tasks such as:

* Tracking each item in the cart, including the quantity and base cost
* Determining estimated shipping costs
* Calculating subtotals, computing additional costs, applying coupons, and determining the payment method

## Query
Use the `Cart` query to retrieve information about a particular cart.

### Syntax

`cart: Cart`

### Cart attributes
The cart object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`applied_coupon` | code | Contains the coupon code if used 
`billing_address` | [CartAddress](#cartAddressAttributes) | Contains the billing address specified in the customer's cart
`cart_id` | String | The unique ID that identifies the customer's cart
`items` | [CartItemInterface](#cartItemsInterface) | Contains the items in the customer's cart
`shipping_addresses` | [CartAddress] | Contains one or more shipping addresses

### Cart address attributes {#cartAddressAttributes}
The cart address object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`address_type` | [AddressTypeEnum] | Specifies if the type of address is shipping or billing
`cart_items` | [CartItemQuantity] | An array of the cart item IDs and quantity of each item
`city` | String | The city specified for the shipping or billing address 
`company` | String | The company specified for the shipping or billing address
`country` | [CartAddressCountry] | The country code and label for the shipping or billing address
`customer_notes` | String | Comments made to the customer that accompanies the order
`firstname` | String | The customer's first name
`items_weight` | Float | The total weight of the items in the cart
`lastname` | String | The customer's last name
`postcode` | String | The postal code for the shipping or billing address
`region` | [CartAddressRegion] | An object containing the region name, region code, and region ID
`street` | [String] | The street for the shipping or billing address
`telephone` | String | The telephone number for the shipping or billing address

### Cart item interface attributes {#cartItemsInterface}
The cart item interface object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`id` | String | ID of the item
`product` | [ProductInterface]({{ page.baseurl }}/graphql/reference/product-interface-implementations.html) | Contains attributes that are common to all types of products
`qty` | Float | The number of items in the cart

<!--
### Add configurable items to cart

The following fields describe the `AddConfigurableProductsToCart` attributes.

Attribute |  Data Type | Description
--- | --- | ---
`cartItems` | [ConfigurableProductCartItemInput] | Contains either shipping or billing information
`cart_id` | String | The customer's email address

### Configurable product cart item input

The following fields describe the `ConfigurableProductCartItemInput` attributes.

Attribute |  Data Type | Description
--- | --- | ---
`customizable_options` | [ConfigurableProductCartItemInput] | Contains either shipping or billing information
`data` | String | The customer's email address
`variant_sku` | String | The customer's email address
-->

### Example usage

The following returns information about a cart given a `cart_id`. Note that the `cart_id` specified is for demonstration purposes only. You will need to [generate](#createEmptyCart) your own `cart_id` for this example to work.

**Request**

``` text
{
  cart(cart_id: "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C") {
    cart_id
    billing_address {
      lastname,
      firstname,
      postcode
     }
    items {
      id,
      qty
    }
    shipping_addresses {
			company
      postcode
      lastname
      firstname      
    }
  }
}
```
**Response**

```json
{
  "data": {
    "cart": {
      "cart_id": "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C",
      "billing_address": {
        "lastname": "Roll",
        "firstname": "Bob",
        "postcode": "78759"
      },
      "items": [
        {
          "id": "22",
          "qty": 1
        }
      ],
      "shipping_addresses": [
        {
          "company": "Magento",
          "postcode": "78759",
          "lastname": "Roll",
          "firstname": "Bob"
        }
      ]
    }
  }
}
```

## Mutations
Use the `quote` mutations to create a cart, apply or remove a coupon from a cart, add products to a cart, and set shipping and billing information to a cart.

### Create an empty cart {#createEmptyCart}

The `createEmptyCart` mutation creates an empty shopping cart for a guest or logged in customer. If you are creating a cart for a logged in customer, you must include the customer's authorization token in the header of the request.

**Syntax**

`mutation: createEmptyCart`

### Example usage

The following call creates a new, empty shopping cart.

**Request**

``` text
mutation {
  createEmptyCart
}
```

**Response**

The response is the quote ID, which is sometimes called the cart ID. The remaining examples in this topic will use this cart ID.

```json
{
  "data": {
    "createEmptyCart": "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C"}
  }
}
```

### Adding and removing coupons from a cart
{:.no_toc}

You can use mutations to add or remove coupons from a specified cart.

### Coupon attributes
The coupon object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`cart_id` | String | The unique ID that identifies the customer's cart
`coupon_code` | String | The coupon code

### Apply coupon to cart

Adds a coupon code to a cart.

#### Syntax

`mutation: applyCouponToCart`

#### Example usage

The following call adds a coupon code called `test2019` to a cart.

**Request**

``` text
mutation {
  applyCouponToCart(input: {cart_id: "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C", coupon_code: "test2019"}) {
    cart {
      applied_coupon {
        code
      }
    }
  }
}
```

**Response**

```json
{
  "data": {
    "applyCouponToCart": {
      "cart": {
        "applied_coupon": {
          "code": "test2019"
        }
      }
    }
  }
}

```

### Remove coupon from cart

Removes a coupon from the specified cart.

#### Syntax

`mutation: removeCouponFromCart`

#### Example usage

The following example removes a coupon from the cart.

**Request**

``` text
mutation {
  removeCouponFromCart(input: {cart_id: "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C"}) {
    cart {
      applied_coupon {
        code
      }
    }
  }
}
```

**Response**

```json
{
  "data": {
    "removeCouponFromCart": {
      "cart": {
        "applied_coupon": {
          "code": "test2019"
        }
      }
    }
  }
}
```

### Adding products to cart
{:.no_toc}

Adds simple items to a specific cart.

### Add simple products to cart attributes
The Add simple products to cart object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`cartItems` | [SimpleProductCartItemInput] | The list of items to add to the cart
`cart_id` | String | The unique ID that identifies the customer's cart

#### Syntax

`mutation: addSimpleProductsToCart`

#### Example usage

The following example adds two Joust Duffle Bags to the cart.

**Request**

``` text
mutation {  
  addSimpleProductsToCart(
    input: {
      cart_id: "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C", 
      cartItems: [
        {
          data: {
            qty: 2
            sku: "24-MB01"
          }
        }
      ]
    }
  ) {
    cart {
      cart_id
    }
  }
}
```

**Response**

```json
{
  "data": {
    "addSimpleProductsToCart": {
      "cart": {
        "cart_id": "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C"
      }
    }
  }
}
```
<!--
## Add configurable products to cart

**THIS DOES NOT SEEM TO BE IMPLEMENTED YET**

**Request**

```text

```

**Response**

```json

```-->

### Updating billing and shipping information
{:.no_toc}

You can set the billing and shipping addresses on a cart and specify shipping methods.

### SetBillingAddressOnCart attributes
The Set billing address on cart object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`billing_address` | [BillingAddressInput](#billingAddressInput) | The list of items to add to the cart
`cart_id` | String | The unique ID that identifies the customer's cart

### SetBillingAddressInput attributes {#billingAddressInput}
The Set billing address input object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`address` | [CartAddressInput](#cartAddressInput) | The list of items to add to the cart
`customer_address_id` | Int | The unique ID that identifies the customer's address
`use_for_shipping` | Boolean | Specifies whether to use the billing address for the shipping address (`True`/`False`)

### CartAddressInput attributes {#cartAddressInput}
The Cart address input object can contain the following attributes:

Attribute |  Data Type | Description
--- | --- | ---
`city` | String | The city specified for the billing address 
`company` | String | The company specified for the billing address
`country_code` | String | The country code and label for the billing address
`customer_notes` | String | Comments made to the customer that accompanies the order
`firstname` | String | The customer's first name
`lastname` | String | The customer's last name
`postcode` | String | The postal code for the billing address
`region` | String | The region code and label for the billing address
`save_in_address_book` | Boolean | Specifies whether to save the address (`True`/`False`)
`street` | [String] | The street for the billing address
`telephone` | String | The telephone number for the billing address

### Set billing address on cart

Use the `setBillingAddressOnCart` mutation to set a new billing address for a specific cart.

#### Syntax

`mutation: setBillingAddressOnCart`

#### Example usage

The following example creates a new billing address for a specific cart.

**Request**

``` text
mutation {
  setBillingAddressOnCart(
    input: {
      cart_id: "4JQaNVJokOpFxrykGVvYrjhiNv9qt31C"
      billing_address: {
         address: {
          firstname: "test firstname"
          lastname: "test lastname"
          company: "test company"
          street: ["test street 1", "test street 2"]
          city: "test city"
          region: "test region"
          postcode: "887766"
          country_code: "US"
          telephone: "88776655"
          save_in_address_book: false
         }
      }
    }
  ) {
    cart {
      billing_address {
        firstname
        lastname
        company
        street
        city
        postcode
        telephone
      }
    }
  }
}
```

**Response**

```json
{
  "data": {
    "setBillingAddressOnCart": {
      "cart": {
        "billing_address": {
          "firstname": "test firstname",
          "lastname": "test lastname",
          "company": "test company",
          "street": [
            "test street 1",
            "test street 2"
          ],
          "city": "test city",
          "postcode": "887766",
          "telephone": "88776655"
        }
      }
    }
  }
}
```

### Set shipping address on cart

Use the `setShippingAddressesOnCart` mutation to set a new shipping address for a specific cart.

#### Syntax

`mutation: setShippingAddressOnCart`

#### Example usage

The following example creates a new shipping address for a specific cart.

**Request**

``` text
mutation {
  setShippingAddressesOnCart(
    input: {
      cart_id: "OOJVZU6tSSQ8vLoXqx9pDMZili3uQ8Hi"
      shipping_addresses: [
        {
          address: {
            firstname: "test firstname"
            lastname: "test lastname"
            company: "test company"
            street: ["test street 1", "test street 2"]
            city: "test city"
            region: "test region"
            postcode: "887766"
            country_code: "US"
            telephone: "88776655"
            save_in_address_book: false
          }
        }
      ]
    }
  ) {
    cart {
      shipping_addresses {
        firstname
        lastname
        company
        street
        city
        postcode
        telephone
      }
    }
  }
}
```

**Response**

```json
{
  "data": {
    "setShippingAddressesOnCart": {
      "cart": {
        "shipping_addresses": [
          {
            "firstname": "test firstname",
            "lastname": "test lastname",
            "company": "test company",
            "street": [
              "test street 1",
              "test street 2"
            ],
            "city": "test city",
            "postcode": "887766",
            "telephone": "88776655"
          }
        ]
      }
    }
  }
}
```

<!--
### Set shipping method on cart
**CAN'T GET THIS TO WORK**

**Request**

``` text
mutation {
  setShippingMethodsOnCart(input: 
    {
      cart_id: "OOJVZU6tSSQ8vLoXqx9pDMZili3uQ8Hi", 
      shipping_methods: [
        {
          shipping_method_code: "flatrate"
          shipping_carrier_code: "UPS"
          cart_address_id: 1
        }
      ]}) {
    
    cart {
      cart_id,
      addresses {
        selected_shipping_method {
          code
          label
        }
      }
    }
  }
}
```

**Response**
-->