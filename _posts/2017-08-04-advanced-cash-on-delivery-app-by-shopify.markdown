---
layout: post
title: "[Solved] Advanced Cash On Delivery App By Shopify"
date: 2017-08-04
author: Swathi Venkatachala
comments: true
tags:
- ecommerce
- shopify
- app
- COD
- Advanced Cash On Delivery
---

Shopify had earlier released an app called `Advanced Cash On Delivery App` which is crafted for
Indian merchants, where COD is widely used payment method. The app is enriched with features like

- set a price range for cash on delivery orders.
- specify the postal codes where you want to offer cash on delivery.
- create custom shipping rates to all cash on delivery orders.
- enter a custom thank you message for cash on delivery orders.
- add an availability checker to your store's theme and allow your customers to check Cash on Delivery eligibility for their postal code. 

[Source](https://help.shopify.com/manual/apps/apps-by-shopify/cash-on-delivery)

### Issue with Advanced Cash On Delivery

So, what is the issue with Advanced Cash On Delivery app?

There are three stages in the checkout for Shopify.

- Customer Information
- Shipping Method
- Payment Method

At the shipping method stage, there is a choice for the customer to choose the shipping method which can be 

- Standard Shipping
- COD Shipping 

If a customer chooses `COD Shipping` which includes some COD handling charges of say Rs.100, post this stage, the customer is taken to the Payment Method stage. Here, the choices shown are `Online Payment Method` like Credit Card, Net Banking, Debit Card, Wallets etc. along with `Cash On Delivery` as another option. This may be confusing to the customer, asking them to choose one of the payment method again. We did encounter quite a lot of customers who were stuck at this stage and not everybody is tech-savvy.

Inorder to customzie the checkout flow we have to be a Shopify Plus customer and not every has that Plan.
So this is what we did - a hack!!

Refer the following images for the current scenario.

![ShippingMethod-1](/img/ShippingMethod-1.png){: .center-image .responsive-image }


![PaymentMethod-1](/img/PaymentMethod-1.png){: .center-image .responsive-image }


![ShippingMethod-2](/img/ShippingMethod-2.png){: .center-image .responsive-image }


![PaymentMethod-2](/img/PaymentMethod-2.png){: .center-image .responsive-image }


### Solution

Refer the following images for the corrected flow of control.

![ShippingMethod-3](/img/ShippingMethod-3.png){: .center-image .responsive-image }


![PaymentMethod-3](/img/PaymentMethod-3.png){: .center-image .responsive-image }


![ShippingMethod-4](/img/ShippingMethod-4.png){: .center-image .responsive-image }


![PaymentMethod-4](/img/PaymentMethod-4.png){: .center-image .responsive-image }



Additionally, we have removed applying discount code on the final stage - `PaymentMethod`.
We have to insert the following code in the admin console. 
Goto `Sales Channels` > `Online Store` > `Preferences` > `Google Analytics` > `Additional Google Analytics Javascript`. In the `<script></script>`, please add the following snippet. 

> Note :
If you have google analytics script already, please make sure that you enter the following script after that.

Customization required in the script :

- Shipping Method Name

Our Shipping Method names looks like `COD Shipping · Rs. 60.00` and `Standard Shipping · Rs. 40.00`. Please make changes accordingly in `if(shippingMethod == 'COD Shipping · Rs. 60.00') {` and `if(shippingMethod == 'Standard Shipping · Rs. 40.00') {`

- Payment Gateway Numbers

If you right click on the page of image `PaymentMethod-4` and say `Inspect Element`, you can fetch the gateway number.Look for `data-subfields-for-gateway=` number. This will be the number related to `Online Payment Method`. In our case it is `122746626`, you have to replace just the number with your payment gateway number for online payment in 4 places. There will be one more number associated with `Cash On Delivery`. In our case, it is `122299010`. You may have to replace that number with your gateway number for COD in 3 places of the script.

{% highlight sh %}
if(document.URL.indexOf('?previous_step=shipping_method&step=payment_method')>=0) {

[].forEach.call(document.querySelectorAll(".order-summary__section--discount"),function(e){
  			e.parentNode.removeChild(e);
		});

[].forEach.call(document.querySelectorAll(".applied-reduction-code__clear-button"),function(e){
  			e.parentNode.removeChild(e);
		});

[].forEach.call(document.querySelectorAll(".field__input-btn-wrapper"),function(e){
  			e.parentNode.removeChild(e);
		});		

var shippingMethod = document.getElementsByClassName('review-block__content')[2].innerText;
console.log(shippingMethod);
if(shippingMethod == 'COD Shipping · Rs. 60.00') {
console.log("COD enabled. Card Payment disabled");

[].forEach.call(document.querySelectorAll("[data-select-gateway='122746626']"),function(e){
  e.parentNode.removeChild(e);
});
[].forEach.call(document.querySelectorAll("[data-subfields-for-gateway='122746626']"),function(e){
  e.parentNode.removeChild(e);
});

document.getElementById("checkout_payment_gateway_122299010").checked = true;
document.getElementById("checkout_payment_gateway_122746626").checked = false;
}

if(shippingMethod == 'Standard Shipping · Rs. 40.00') {
console.log("Card Payment enabled. COD disabled.");

[].forEach.call(document.querySelectorAll("[data-select-gateway='122299010']"),function(e){
  e.parentNode.removeChild(e);
});

document.getElementById("checkout_payment_gateway_122299010").checked = false;
document.getElementById("checkout_payment_gateway_122746626").checked = true;

}
}
{% endhighlight %} 

If you are technically challenged or have issues in setting this up, feel free to shoot an email to 
`swatzdev@gmail.com` to fix this flow.

Happy Shipping! :)
