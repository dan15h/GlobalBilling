# GlobalBilling
Global Billing Api is a wrapper on Google Play billing Library. It helps you easily integrate In App
purchases and Subscription in your android Application.


## Getting Started

## Step 1

Add maven repository in project level build.gradle or in latest project setting.gradle file

```kotlin 

    repositories {
        google()
        mavenCentral()
        maven { url "https://jitpack.io" }
    }
 
```  

## Step 2

Add Global Billing Helper to App level build.gradle.

```kotlin

dependencies {
    implementation ''
}

```  

## Step 3

Fetch the product early in your application

```kotlin 
    GlobalBilling(this)
    .setSubKeys(mutableListOf("your-sub-key1", "your-sub-key2")) //Subscription keys 
    .setInAppKeys(mutableListOf("your-inapp-key1", "your-inapp-key2", "your-consumable-inapp-key1")) //In App product keys
    .setConsumableKeys(mutableListOf("your-consumable-inapp-key1")) //Consumable product keys.
    .enableLogging() //Enable logs for realease builds
    .setBillingClientListener(object : BillingClientListener {
        override fun onClientReady() {
            //Client is ready, all prices fetched.
        }

        override fun onClientInitError() {
            //Client failed, check cause and try again.
        }
    })
```
⚠️ **Note: Consumable key must be added to both lists ```setInAppKeys()```
and ```setConsumableKeys()```** ⚠️


### Get product prices with ```getAllProductPrices()```
It return list if ProductPriceInfo

```kotlin
  val prices = mBilling.getAllProductPrices()
  prices.forEach { productPriceInfo ->

  }
```

Get In-App Product price

```kotlin
    GlobalBilling(this).getProductPriceByKey("In-App Key").price
```

Get specific subscription price (without offer)

```kotlin
    GlobalBilling(this).getProductPriceByKey("Base Plan ID", "").price
```

Get specific subscription price (with offer)

```kotlin
    GlobalBilling(this).getProductPriceByKey("Base Plan ID", "Offer ID").price
```

### Buy In-App Product

```kotlin
    mBilling.buyInApp(activity, "In-App Key", false)
```

```fasle```  value used for **isPersonalizedOffer** attribute:

If your app can be distributed to users in the European Union, use the **isPersonalizedOffer**
value ```true``` to disclose to users that an item's price was personalized using automated
decision-making.

**Note: It will auto acknowledge the In-App purchase and provides callback upon product acknowledgement.**

### Subscribe to a Subscription

```kotlin
    mBilling.subscribe(activity, "Base Plan ID")
```

Subscribe to a offer

```kotlin
    mBilling.subscribe(activity, "Base Plan ID", "Offer ID")
```

**Note: It will auto acknowledge the In-App purchase and provides callback upon product acknowledgement.**

### Upgrade or Downgrade Subscription

 ```kotlin
    mBilling.upgradeOrDowngradeSubscription(
    this,
    "New Base Plan ID",
    "New Offer Id (If offer )",
    "Old Base Plan ID",
    ProrationMode
)

```

```ProrationMode``` is a setting in subscription billing systems that determines how proration is
calculated when changes are made to a subscription plan. There are different proration modes,
including:

```
  1. DEFERRED : Replacement takes effect when the old plan expires, and the new price will be charged at the same time.

  2. IMMEDIATE_AND_CHARGE_FULL_PRICE : Replacement takes effect immediately, and the user is charged full price of new plan and is given a      full billing cycle of subscription, plus remaining prorated time from the old plan.

  3. IMMEDIATE_AND_CHARGE_PRORATED_PRICE : Replacement takes effect immediately, and the billing cycle remains the same.

  4. IMMEDIATE_WITHOUT_PRORATION : Replacement takes effect immediately, and the new price will be charged on next recurrence time.

  5. IMMEDIATE_WITH_TIME_PRORATION : Replacement takes effect immediately, and the remaining time will be prorated and credited to the          user.

  6. UNKNOWN_SUBSCRIPTION_UPGRADE_DOWNGRADE_POLICY
```

Example :

```kotlin
  GlobalBilling(this).upgradeOrDowngradeSubscription(
    this,
    "New Base Plan ID",
    "New Offer Id (If offer )",
    "Old Base Plan ID",
    BillingFlowParams.ProrationMode.IMMEDIATE_AND_CHARGE_FULL_PRICE
)
```

### Billing Listeners

Interface implementation to handle purchase results and errors.

 ```kotlin

GlobalBilling(this).setBillingEventListener(object : BillingEventListener {
    override fun onProductsPurchased(purchases: List<Purchase?>) {
        //call back when purchase occured 
    }

    override fun onPurchaseAcknowledged(purchase: Purchase) {
        //call back when purchase occur and acknowledged 
    }

    override fun onPurchaseConsumed(purchase: Purchase) {
        //call back when purchase occur and consumed 
    }

    override fun onBillingError(error: ErrorType) {
        when (error) {
            ErrorType.CLIENT_NOT_READY -> {

            }
            ErrorType.CLIENT_DISCONNECTED -> {

            }
            ErrorType.PRODUCT_NOT_EXIST -> {

            }
            ErrorType.BILLING_ERROR -> {

            }
            ErrorType.USER_CANCELED -> {

            }
            ErrorType.SERVICE_UNAVAILABLE -> {

            }
            ErrorType.BILLING_UNAVAILABLE -> {

            }
            ErrorType.ITEM_UNAVAILABLE -> {

            }
            ErrorType.DEVELOPER_ERROR -> {

            }
            ErrorType.ERROR -> {

            }
            ErrorType.ITEM_ALREADY_OWNED -> {

            }
            ErrorType.ITEM_NOT_OWNED -> {

            }

            ErrorType.SERVICE_DISCONNECTED -> {

            }

            ErrorType.ACKNOWLEDGE_ERROR -> {

            }

            ErrorType.ACKNOWLEDGE_WARNING -> {

            }

            ErrorType.OLD_PURCHASE_TOKEN_NOT_FOUND -> {

            }

            ErrorType.CONSUME_ERROR -> {

            }
            else -> {

            }
        }
    }
})

```


This method return ```ProductPriceInfo``` object that contain complete detail about subscription. To
get only price just call ```.Price```.

### Get Single Product Detail

For In-App Product

```kotlin
    GlobalBilling(this).getProductDetail("In-App Key", "", BillingClient.ProductType.INAPP)
```

For Subs Product

```kotlin
    GlobalBilling(this).getProductDetail(
    "Base Plan ID",
    "Offer ID",
    BillingClient.ProductType.SUBS
)
```

Above methods return ```ProductPriceInfo``` object that contain complete detail about Product.

## Step 5 (Check if any Product buy)

### Check In-App

For check if user buy any In-App Product

 ```kotlin
  GlobalBilling(this).isInAppPremiumUser()

 ``` 

For check specific In-App Product

``` kotlin
  GlobalBilling(this).isInAppPremiumUserByInAppKey("In-App Key")

 ```

### Check Subscription

For check if any subscription is subscribe

 ```kotlin
  GlobalBilling(this).isSubsPremiumUser()

 ``` 

For check if any specific subscription is subscribe (by Base Plan ID)

``` kotlin
  GlobalBilling(this).isSubsPremiumUserByBasePlanKey("Base Plan ID")

 ``` 

For check if any specific subscription is subscribe (by Subscription ID)

``` kotlin
  GlobalBilling(this).isSubsPremiumUserBySubIDKey("Subscription ID")

 ``` 

## Step 6 (Cancel any subscription)

### Cancel  Subscription

```kotlin
GlobalBilling(this).unsubscribe(this, "Subscription ID")
```

## Step 7 (Other Utils for Billing)

### Handle pending purchases

to check and handle pending purchase call this below method on activity ```OnResume()``` method.

```kotlin
GlobalBilling(this).fetchActivePurchases()
```

**Note: Call this method in background thread. Use Coroutine to run this method in background.**

### Check subscription support

```kotlin
GlobalBilling(this).areSubscriptionsSupported()

```

### Check Billing Client is Ready

```kotlin
GlobalBilling(this).isClientReady()

```

### Release billing client object

Call this method when app close or when billing not needed any more.

```kotlin
GlobalBilling(this).release()

```
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT
NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES
OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

