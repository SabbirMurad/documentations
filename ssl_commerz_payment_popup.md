### 1. Add this codeblock in your checkout page before the end of body tag

```html
  <script>
    (function (window, document) {
      var loader = function () {
          var script = document.createElement("script"), tag = document.getElementsByTagName("script")[0];
          script.src = "https://sandbox.sslcommerz.com/embed.min.js?" + Math.random().toString(36).substring(7);
          tag.parentNode.insertBefore(script, tag);
      };
    
      window.addEventListener ? window.addEventListener("load", loader, false) : window.attachEvent("onload", loader);
    })(window, document);
  </script>
```

### 2. Use the start session api of ssl commerz as it was intended
  - save the gateway url and store logo in your database
  - send the transaction id of the purchase to frontend in the response

### 4. In frontend after calling the transaction start api create a button
```js
  let button = document.createElement('button')
  button.classList.add('.your-button-class')
  button.id = 'sslczPayBtn'
  // the transaction id you get from your transaction starting api
  button.setAttribute('order', response.data.transaction_id)

  // the api route you will create in step 5
  button.setAttribute('endpoint', 'api route')
```
 - append this button in your page where you see appropriate

# 5. Create a api route which will serve the gateway url to the ssl button

 - for form_data use `web::Form<YourStructName>`
 - keep a field named `order` in that struct
 - find the gateway url and store logo from database using the order id which is actually the transaction id that you set with the button as attribute
 - create another struct containing this 3 fields
 ```rust
  pub struct SendData {
    status: String,
    data: String,
    logo: String,
  }

  let data = SendData {
    status: "success".to_string(),
    data: purchase.gateway_url,
    logo: purchase.store_logo
  };
 ```
 - here status should be `success`, data is the `gateway url` and logo is the `store logo`
 - then send the data in the response like shown below
 ```rust
  Ok(HttpResponse::Ok().json(data))
 ```