# Automating Lionfish

You can automate everything in LionFish's web interface using our API. You will find documentation on how to authenticate and some example common operations. If you want to do something that is not documented you can inspect the network panel in your web browser while using the web interface or contact support.

## API Access

> Example request that grants an access token

```shell
curl -H "Content-Type:application/json" -d '{"api_key": "API_KEY_HERE"}' \
"https://www.lionfishdata.com/v0/user/YOUR_ACCOUNT_ID/api_login"
```

Go to the [API access page](https://www.lionfishdata.com/user/-/api) and copy Account ID and API Key.  If you lose your API Key or accidentally expose it, click "Generate API key" and it will be revoked and a new key generated. If you loose your API Key we can not recover it and you will have to generate a new one.

This API key cannot be used directly. Instead, you need to exchange it for a short-lived security token, which can then be used in our API. See the request to the right for an example.

> Example request that includes an `Authorization` header

```shell
curl -H "Authorization: Bearer YOUR_TOKEN_VALUE" -H "Content-Type:application/json" \
"https://www.lionfishdata.com/v0/user/YOUR_ACCOUNT_ID/lists"
```

You will get back json which contains a `token` key. Include this token in an `Authorization: Bearer YOUR_TOKEN_VALUE` header to all API requests.

## List your lists

> Example request that returns all your lists

```shell
curl -H "Authorization: Bearer YOUR_TOKEN_VALUE" -H "Content-Type:application/json" \
"https://www.lionfishdata.com/v0/user/YOUR_ACCOUNT_ID/lists"
```

> Example response of all your lists

```shell
{
  "count": 50,
  "data": [
    {
      "created_time": "2017-07-24T19:28:11.464760+00:00",
      "frontend_details": {
        "description": "Just a list"
      },
      "id": "F9pfzK3rsvCyZsgHrnbpvW",
      "name": "Test Products",
      "num_products": 202,
    },
    {
      "created_time": "2017-08-15T21:02:03.850895+00:00",
      "frontend_details": {},
      "id": "Kue8WUQNTSn5tqJ3EvTkQx",
      "name": "My list",
      "num_products": 11,
    }
  ],
  "offset": 0,
  "total_count": 2,
  "url": "https://www.lionfishdata.com/v0/user/YOUR_ACCOUNT_ID/lists"
}
```

Often the first thing you will want to do is get a list of your lists so you can find a particular list's id.

## Updating your list

[Once you have a list's id](/#list-your-lists), its easy to replace the products in a list. **This will remove all existing items!** Replacing the list rather than updating has the benefit that you don't need to keep track of which items were removed or added, just `PUT` them all.

### Standard list

All [retailer codes](/#supported-retailers) are supported.

> Standard list

> Example request that replaces your list's products

```shell
curl -X PUT -H "Authorization: Bearer YOUR_TOKEN_VALUE" -H "Content-Type:application/json" \
 'https://www.lionfishdata.com/v0/user/YOUR_ACCOUNT_ID/lists/LIST_ID/products' \
 -d '{"products":[{"retailer":"amazon","product_id":"B00000J0G7"},{"retailer":"amazon","product_id":"B010S9N6OO"}]}'
```

> Example response

```shell
{
  "num_products": 1,
  "success": true
}
```


### Smart list

Fetch all products from the retailer's product list url and put it in a list.

1. go to retailer's website.
2. search for the interested category, apply filters if needed.
3. copy the url in location bar.


Technically, all the urls are supported that have paginator at the bottom.


Only **amazon** (US) retailer is supported.

> Smart list

> Example request that replaces your list's products

```shell
curl -X POST -H "Authorization: Bearer YOUR_TOKEN_VALUE" -H "Content-Type:application/json" \
 'https://www.lionfishdata.com/v0/user/YOUR_ACCOUNT_ID/lists/SMART_LIST_ID/run' \
 -d '{ "output_type": "email", "output_address": YOUR_EMAIL_ADDRESS, "job_type": "smartlist", "output_transformer": { "name": "LineSeparatedJson" } }'
```

> Example response

```shell
{
  "completed": 0,
  "created_time": "2018-07-23T15:19:12.634358+00:00",
  "deadline_time": "2018-07-23T16:19:12.636425+00:00",
  "failure_reason": null,
  "finish_time": null,
  "id": "D2LWmDsRrC44eapWBBDZhT",
  "job_type": "smartlist",
  "list_id": SMART_LIST_ID,
  "output_address": YOUR_EMAIL_ADDRESS,
  "output_transformer": {
    "name": "LineSeparatedJson"
  },
  "output_type": "email",
  "progress": null,
  "queued_jobs": 0,
  "results_url": null,
  "schedule_id": null,
  "start_time": "2018-07-23T15:19:12.636425+00:00",
  "state": "processing",
  "user_id": YOUR_ACCOUNT_ID
}

```
