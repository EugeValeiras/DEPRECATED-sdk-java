# MercadoPago SDK module for Payments integration

* [Install](#install)
* [Basic checkout](#basic-checkout)
* [Customized checkout](#custom-checkout)
* [Generic methods](#generic-methods)

<a name="install"></a>
## Install

Install it using maven:

Just add to your pom.xml the following repository

```XML
<repositories>
    ...
    <repository>
        <id>mercadopago</id>
        <url>https://github.com/mercadopago/sdk-java/raw/master/releases</url>
    </repository>
    ...
</repositories>  
```

Then add the dependency

```XML
 <dependencies>
    ...
    <dependency>
        <groupId>com.mercadopago</groupId>
        <artifactId>sdk</artifactId>
        <version>0.3.4</version>
    </dependency>
    ...
</dependencies>
```
And that's it!

<a name="basic-checkout"></a>
## Basic checkout

### Configure your credentials

* Get your **CLIENT_ID** and **CLIENT_SECRET** in the following address:
    * Argentina: [https://www.mercadopago.com/mla/herramientas/aplicaciones](https://www.mercadopago.com/mla/herramientas/aplicaciones)
    * Brazil: [https://www.mercadopago.com/mlb/ferramentas/aplicacoes](https://www.mercadopago.com/mlb/ferramentas/aplicacoes)
    * México: [https://www.mercadopago.com/mlm/herramientas/aplicaciones](https://www.mercadopago.com/mlm/herramientas/aplicaciones)
    * Venezuela: [https://www.mercadopago.com/mlv/herramientas/aplicaciones](https://www.mercadopago.com/mlv/herramientas/aplicaciones)
    * Colombia: [https://www.mercadopago.com/mco/herramientas/aplicaciones](https://www.mercadopago.com/mco/herramientas/aplicaciones)
    * Chile: [https://www.mercadopago.com/mlc/herramientas/aplicaciones](https://www.mercadopago.com/mlc/herramientas/aplicaciones)

```JAVA
import com.mercadopago.MP;

import org.codehaus.jettison.json.JSONException;
import org.codehaus.jettison.json.JSONObject;

MP mp = new MP ("CLIENT_ID", "CLIENT_SECRET");

```

### Preferences

#### Get an existent Checkout preference

```JAVA
JSONObject preference = mp.getPreference("PREFERENCE_ID");

System.out.println(preference.toString());
```

#### Create a Checkout preference

```JAVA
JSONObject createPreferenceResult = mp.createPreference("{'items':[{'title':'Prueba','quantity':1,'currency_id':'ARS','unit_price':10.5}]}");
System.out.println(createPreferenceResult.toString());
```

#### Update an existent Checkout preference

```JAVA
JSONObject updatePreferenceResult = mp.updatePreference("PREFERENCE_ID", "{'items':[{'title':'Prueba','quantity':1,'currency_id':'USD','unit_price':2}]}");
System.out.println(updatePreferenceResult.toString());
```

### Payments/Collections

#### Search for payments

```JAVA
// Sets the filters you want
Map<String, Object> filters = new HashMap<String, Object> ();
   filters.put("site_id", "MLA"); // Argentina: MLA; Brasil: MLB; Mexico: MLM; Venezuela: MLV; Colombia: MCO
   filters.put("external_reference", "Bill001");
        
// Search payment data according to filters
JSONObject searchResult = mp.searchPayment (filters);
JSONArray results = searchResult.getJSONObject("response").getJSONArray("results");

for (int i = 0; i < results.length(); i++) {
    System.out.println(results.getJSONObject(i).getJSONObject("collection").getString("id"));
    System.out.println(results.getJSONObject(i).getJSONObject("collection").getString("external_reference"));
    System.out.println(results.getJSONObject(i).getJSONObject("collection").getString("status"));
}
```

#### Get payment data

```JAVA
// Get the payment reported by the IPN. Glossary of attributes response in https://developers.mercadopago.com
JSONObject payment_info = mp.getPayment("ID");

// Show payment information
if (Integer.parseInt (payment_info.get("status").toString()) == 200) {
    out.print(payment_info.get("response"));
}
```

### Cancel (only for pending payments)

```JAVA
JSONObject result = mp.cancelPayment(request.getParameter("ID"));

// Show result
out.print(result);
```

### Refund (only for accredited payments)

```JAVA
JSONObject result = mp.refundPayment(request.getParameter("ID"));

// Show result
out.print(result);
```

<a name="custom-checkout"></a>
## Customized checkout

### Configure your credentials

* Get your **ACCESS_TOKEN** in the following address:
    * Argentina: [https://www.mercadopago.com/mla/account/credentials](https://www.mercadopago.com/mla/account/credentials)
    * Brazil: [https://www.mercadopago.com/mlb/account/credentials](https://www.mercadopago.com/mlb/account/credentials)
    * Mexico: [https://www.mercadopago.com/mlm/account/credentials](https://www.mercadopago.com/mlm/account/credentials)
    * Venezuela: [https://www.mercadopago.com/mlv/account/credentials](https://www.mercadopago.com/mlv/account/credentials)
    * Colombia: [https://www.mercadopago.com/mco/account/credentials](https://www.mercadopago.com/mco/account/credentials)

```JAVA
import com.mercadopago.MP;

import org.codehaus.jettison.json.JSONException;
import org.codehaus.jettison.json.JSONObject;

MP mp = new MP ("ACCESS_TOKEN");

```

### Create payment

```JAVA
mp.post ("/v1/payments", paymentData);
```

### Create customer

```JAVA
mp.post ("/v1/customers", "{'email': 'email@test.com'}");
```

### Get customer

```JAVA
mp.get ("/v1/customers/CUSTOMER_ID");
```

* View more Custom checkout related APIs in Developers Site
    * Argentina: [https://labs.mercadopago.com.ar/developers](https://labs.mercadopago.com.ar/developers)
    * Brazil: [https://labs.mercadopago.com.br/developers](https://labs.mercadopago.com.br/developers)
    * Mexico: [https://labs.mercadopago.com.mx/developers](https://labs.mercadopago.com.mx/developers)
    * Venezuela: [https://labs.mercadopago.com.ve/developers](https://labs.mercadopago.com.ve/developers)
    * Colombia: [https://labs.mercadopago.com.co/developers](https://labs.mercadopago.com.co/developers)

<a name="generic-methods"></a>
## Generic methods

You can access any other resource from the MercadoPago API using the generic methods:

```JAVA
// Get a resource, with optional URL params. Also you can disable authentication for public APIs
mp.get ("/resource/uri", [params], [authenticate=true]);

// Create a resource with "data" and optional URL params.
mp.post ("/resource/uri", data, [params]);

// Update a resource with "data" and optional URL params.
mp.put ("/resource/uri", data, [params]);

// Delete a resource with optional URL params.
mp.delete ("/resource/uri", [params]);
```

 For example, if you want to get the Sites list (no params and no authentication):

```JAVA
JSONObject result = mp.get ("/sites", null, false);

out.print(result);
```
