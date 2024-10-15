## Email Enrichment Logic

We have three keys:

1.  perEmailEnrichment (number)
2.  totalEmailEnrichment (number)
3.  skipCalculation (boolean)

### 1) perEmailEnrichment

```json

{
  ...config,
  "resourceConsumption" : {
    "perEmailEnrichment": 1
  },
  ...config,
}

```

creditsToDeduct = `perEmailEnrichment` \* `number of emails found`

Explanation:

- We will calcualte the number of emails found and then multiply it with perEmailEnrichment to get the credits to deduct.

#### Usecase:

Li Peopple Search Export, Li Company Employees Export,etc. (automations that return array of objects)

### 2) totalEmailEnrichment

```json

{
  ...config,
  "resourceConsumption" : {
    "totalEmailEnrichment": 1
  },
  ...config,
}

```

creditsToDeduct = number of emails found > 0 ? `totalEmailEnrichment` : 0

Explanation:

- Here, if we have more than one email found, we will deduct the number mentioned in `totalEmailEnrichment` regardless of how many emails we found.
- If we don't find any email, we deduct `zero` credits
- This won't consider the `perEmailEnrichment` key even if it's mentioned.

#### Usecase:

Find emails using domain (anywhere where we only make one api call but get array of objects as response).

### 3) skipCalculation

```json

{
  ...config,
  "resourceConsumption" : {
    "skipCalculation": true,
    "totalEmailEnrichment": 5
  },
  ...config,
}

```

creditsToDeduct = `totalEmailEnrichment`

#### Explanation:

- Here we directly deduct the `totalEmailEnrichment` key, even if we don't find any emails but the api call has to be successfull

#### Usecase:

- Wherever we want to deduct credits on a success of an api call.
- As we discussed that we would have to use scraperApi for crunchbase automations.

## Some Examples:

1.

```json

{
  ...config,
  "resourceConsumption" : {
    "skipCalculation": true,
    "perEmailEnrichment": 2,
    "totalEmailEnrichment": 5
  },
  ...config,
}

```

Consider: no of emails found = 10

CreditsTodeduct = 5

Exp: As skipcalculation is true, we directly use the totalEmailEnrichment.

2.

```json

{
  ...config,
  "resourceConsumption" : {
    "skipCalculation": true,
    "perEmailEnrichment": 2
  },
  ...config,
}

```

Consider: no of emails found = 10

CreditsTodeduct = 1

Exp: If skipCalculation is true and `totalEmailEnrichment` is not mentioned, we use the default value of 1. `perEmailEnrichment` has no significance incase of `skipCalculation:true`

3.

```json

{
  ...config,
  "resourceConsumption" : {
    "perEmailEnrichment": 2,
    "totalEnrichment": 10
  },
  ...config,
}

```

Consider: no of emails found = 10

CreditsTodeduct = 10

Exp: We found more than zero emails and `totalEmailEnrichment` exists so it takes priority over `perEmailEnrichment`

4.

```json

{
  ...config,
  "resourceConsumption" : {
    "perEmailEnrichment": 2
  },
  ...config,
}

```

Consider: no of emails found = 10

CreditsTodeduct = 20

Exp: As totalEmailEnrichment is not mentioned, we perform the multiplication of no of emails found and `perEmailEnrichment`

5.

```json

{
  ...config,
  "resourceConsumption" : {

  },
  ...config,
}

```

Consider: no of emails found = 10

CreditsTodeduct = 10

Exp: As `totalEmailEnrichment` and `perEmailEnrichment` is not mentioned, we perform the multiplication of no of emails found and 1 (basically meaning perEmailEnrichment is defaulted to 1)
