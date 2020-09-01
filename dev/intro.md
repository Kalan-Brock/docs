<title>Developing on the Invoiced Software, Getting Started | Invoiced</title>
<meta name="description" content="At the heart of Invoiced is a powerful accounts receivable API. It allows you to build integrations with accounting systems, CRMs, and more.">

# Developing on Invoiced

At the heart of Invoiced is a powerful billing API. It allows you to build integrations with accounting systems, CRMs, ordering systems, business intelligence, and any other backoffice systems used to run your business.

The API was modeled after <a href="https://en.wikipedia.org/wiki/Representational_state_transfer">REST</a>. All communication happens over HTTPS with `https://api.invoiced.com`.

## Getting Started

The first step is to determine how you want to connect to the API. We offer client libraries in several languages, including <a href="https://github.com/Invoiced/invoiced-ruby">Ruby</a>, <a href="https://github.com/Invoiced/invoiced-php">PHP</a>, <a href="https://github.com/Invoiced/invoiced-python">Python</a>, <a href="https://github.com/Invoiced/invoiced-java">Java</a>, <a href="https://github.com/Invoiced/invoiced-csharp">.NET</a>, and <a href="https://github.com/Invoiced/invoiced-go">Go</a>. If we don't have a client library for your language then you can perform the HTTP requests to Invoiced directly in the language of your choice.

Once you have your client library ready to go the next step is to get an API key. The API uses [Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) to authenticate requests. All API requests require a valid API key. You can grab an API key by signing in to the dashboard, and then going to **Settings** &rarr; **Developers** &rarr; **API Keys**. Please remember to keep this API key safe. In the wrong hands it could give unwanted access to your Invoiced account.

Next, we are going to walk through a common invoicing workflow.

### Choose your language

<div class="language-selector">
	<a href="#" class="btn btn-link" data-lang="bash">Shell</a>
	<a href="#" class="btn btn-link" data-lang="ruby">Ruby</a>
	<a href="#" class="btn btn-link" data-lang="php">PHP</a>
    <a href="#" class="btn btn-link" data-lang="python">Python</a>
	<a href="#" class="btn btn-link" data-lang="java">Java</a>
</div>

### Creating a Customer

Customers are at the core of everything on Invoiced. Customers represent a billable entity from your perspective, whether this is a person, organization, or account. You must create a customer first before you can invoice or accept payments.

Every customer has an [AutoPay](/resources/docs/payments/autopay) option. When enabled this will charge your customer's connected payment source each billing cycle. When AutoPay is off (the default) we will instead issue an invoice that your customer can pay using any of the payment methods you accept.

```bash
curl "https://api.invoiced.com/customers" \
  -u {API_KEY}: \
  -d name="Acme" \
  -d email="billing@acmecorp.com" \
  -d number="1234" \
  -d payment_terms="NET 30"
```

```ruby
require "invoiced"
invoiced = Invoiced::Client.new("{YOUR_API_KEY}")

customer = invoiced.Customer.create(
  :name => "Acme",
  :email => "billing@acmecorp.com",
  :number => "1234",
  :payment_terms => "NET 30"
)
```

```php
$invoiced = new Invoiced\Client("{YOUR_API_KEY}");

$customer = $invoiced->Customer->create([
  'name' => "Acme",
  'email' => "billing@acmecorp.com",
  'number' => "1234",
  'payment_terms' => "NET 30"
]);
```

```python
import invoiced
client = invoiced.Client("{YOUR_API_KEY}")

customer = client.Customer.create(
  name="Acme",
  email="billing@acmecorp.com",
  number="1234",
  payment_terms="NET 30"
)
```

```java
import com.invoiced.entity.Connection;
import com.invoiced.entity.Customer;

Connection invoiced = new Connection("{YOUR_API_KEY}", false);

Customer customer = invoiced.newCustomer();
customer.name = "Acme";
customer.email = "billing@acmecorp.com";
customer.paymentTerms = "NET 30";
customer.create();
```

The `number` property helps you tie the customer on Invoiced to the ID already used within your system. It is not required as we would generate a value for you if it was not supplied.

We highly recommend saving the customer's Invoiced ID (`id` property) in your own database. This ID is required to retrieve the customer's account, create invoices, and perform any other customer-centric tasks.

### Creating an Invoice

Invoices are another core resource on Invoiced. As you would expect, an invoice represents an amount owed to you by a customer.

```bash
curl "https://api.invoiced.com/invoices" \
  -u {API_KEY}: \
  -d customer={CUSTOMER_ID} \
  -d items[0][name]="Copy paper, Case" \
  -d items[0][quantity]=3 \
  -d items[0][unit_cost]=45 \
  -d items[1][name]="Delivery" \
  -d items[1][quantity]=1 \
  -d items[1][unit_cost]=10 \
  -d taxes[0][amount]=3.85
```

```ruby
invoice = invoiced.Invoice.create(
  :customer => customer.id,
  :items => [
    {
      :name => "Copy paper, Case",
      :quantity => 3,
      :unit_cost => 45
    },
    {
      :name => "Delivery",
      :quantity => 1,
      :unit_cost => 10
    }
  ],
  :taxes => [
    {
      :amount => 3.85
    }
  ]
)
```

```php
$invoice = $invoiced->Invoice->create([
  'customer' => $customer->id,
  'items' => [
    [
      'name' => "Copy paper, Case",
      'quantity' => 3,
      'unit_cost' => 45
    ],
    [
      'name' => "Delivery",
      'quantity' => 1,
      'unit_cost' => 10
    ]
  ],
  'taxes' => [
    [
      'amount' => 3.85
    ]
  ]
]);
```

```python
invoice = client.Invoice.create(
  customer=customer.id,
  items=[
    {
      'name': "Copy paper, Case",
      'quantity': 3,
      'unit_cost': 45
    },
    {
      'name': "Delivery",
      'quantity': 1,
      'unit_cost': 10
    }
  ],
  taxes=[
    {
      'amount': 3.85
    }
  ]
)
```

```java
import com.invoiced.entity.Invoice;
import com.invoiced.entity.LineItem;
import com.invoiced.entity.Tax;

Invoice invoice = invoiced.newInvoice();
invoice.customer = customer.id;
invoice.paymentTerms = "NET 14";
LineItem[] items = new LineItem[2];
items[0] = new LineItem();
items[0].name = "Copy paper, Case";
items[0].quantity = 3D;
items[0].unitCost = 45D;
items[1] = new LineItem();
items[1].name = "Delivery";
items[1].quantity = 1D;
items[1].unitCost = 10D;
invoice.items = items;
Tax[] taxes = new Tax[1];
taxes[0] = new Tax();
taxes[0].amount = 3.85D;
invoice.taxes = taxes;
invoice.create();
```

The invoice will inherit the AutoPay and payment term settings from the customer's profile.

#### Sending Invoices

Now that you have created the invoice you might want to send it to the customer. That's fairly easy to do through the API.

```bash
curl "https://api.invoiced.com/invoices/{INVOICE_ID}/emails" \
  -u {API_KEY}: \
  -X POST
```

```ruby
invoice.send
```

```php
$invoice->send();
```

```python
invoice.send
```

```java
import com.invoiced.entity.Email;
import com.invoiced.entity.EmailRequest;

EmailRequest emailRequest = new EmailRequest();
Email[] emails = invoice.send(emailRequest);
```

The customer will be sent the invoice with a **View Invoice** button using the default email template. You can customize these templates through the dashboard in **Settings** &rarr; **Emails**.

#### Items

Our Items feature allows you to build a simpler, more robust integration by centralizing pricing information for the products and services that you sell. You must first add items through the dashboard in **Settings** &rarr; **Items** or through the Items API. Then you can bill for items by simply referencing them by ID:

```bash
curl "https://api.invoiced.com/invoices" \
  -u {API_KEY}: \
  -d customer={CUSTOMER_ID} \
  -d items[0][catalog_item]="copy_paper_20lb" \
  -d items[0][quantity]=3 \
  -d items[1][catalog_item]="delivery" \
  -d taxes[0][amount]=3.85
```

```ruby
invoiced.Invoice.create(
  :customer => customer.id,
  :items => [
    {
      :catalog_item => "copy_paper_20lb",
      :quantity => 3
    },
    {
      :catalog_item => "delivery"
    }
  ],
  :taxes => [
    {
      :amount => 3.85
    }
  ]
)

```

```php
$invoice = $invoiced->Invoice->create([
  'customer' => $customer->id,
  'items' => [
    [
      'catalog_item' => 'copy_paper_20lb',
      'quantity' => 3
    ],
    [
      'catalog_item' => "delivery"
    ]
  ],
  'taxes' => [
    [
      'amount' => 3.85
    ]
  ]
]);
```

```python
invoice = client.Invoice.create(
  customer=customer.id,
  items=[
    {
      'catalog_item': "copy_paper_20lb",
      'quantity': 3
    },
    {
      'catalog_item': "delivery"
    }
  ],
  taxes=[
    {
      'amount': 3.85
    }
  ]
)
```

```java
import com.invoiced.entity.Invoice;
import com.invoiced.entity.LineItem;
import com.invoiced.entity.Tax;

Invoice invoice = invoiced.newInvoice();
invoice.customer = customer.id;
invoice.paymentTerms = "NET 14";
LineItem[] items = new LineItem[2];
items[0] = new LineItem();
items[0].catalogItem = "copy_paper_20lb";
items[0].quantity = 3D;
items[1] = new LineItem();
items[1].catalogItem = "delivery";
invoice.items = items;
Tax[] taxes = new Tax[1];
taxes[0] = new Tax();
taxes[0].amount = 3.85D;
invoice.taxes = taxes;
invoice.create();
```

Note that we did not have to include the `unit_cost` on the item as it was filled in automatically (although you can override per line item by including it). Another benefit of items is that it ties line items together in reports.


### Recording a Payment

On Invoiced payments are represent with the **Transaction** resource. A transaction models the exchange of value between you and a customer, including payments, refunds, and credits. Whenever customers pay online through the customer portal we automatically create a transaction for the payment. However, if you accept payments outside of Invoiced, always true if you are accepting checks or wire transfers, then you have to record the payment yourself through the dashboard or API.

```bash
curl "https://api.invoiced.com/transactions" \
  -u {API_KEY}: \
  -d invoice={INVOICE_ID} \
  -d method="check" \
  -d gateway_id="1450" \
  -d amount=148.85
```

```ruby
invoiced.Transaction.create(
  :invoice => invoice.id,
  :method => "check",
  :gateway_id => "1450",
  :amount => 148.85
)
```

```php
$invoiced->Transaction->create([
  'invoice' => $invoice->id,
  'method' => "check",
  'gateway_id' => "1450",
  'amount' => 148.85
]);
```

```python
client.Transaction->create(
  invoice=invoice.id,
  method="check",
  gateway_id="1450",
  amount=148.85
)
```

```java
import com.invoiced.entity.Transaction;

Transaction transaction = invoiced.newTransaction();
transaction.invoice = invoice.id;
transaction.method = "check";
transaction.gatewayId = "1450";
transaction.amount = 148.85;
transaction.create();
```

This will record a payment for the invoice we created earlier and mark it as paid. Since this is an offline payment the `gateway_id` property can be used to reference a check #. And that's it for a basic accounts receivable workflow.

### What's next?

The [API docs](/resources/docs/api) explain all of the resources and endpoints available to you. Our other development guides might be useful as well. If you run into any questions or need help with some code then please don't hesitate to contact us. We would love to hear from you.