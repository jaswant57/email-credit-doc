# email-credit-doc

## Email Consumption

### Current Situation:

In the backend system, we deduct **email enrichment credits** based on the `requiredResources` field. By default, **1 email enrichment credit** is deducted, but this can be customized using the `resourceConsumption` field in automation config.

### Default Behavior:

When we specify `"emailEnrichment"` in the `requiredResources` field, the backend automatically deducts 1 credit for each email enrichment execution.

#### Example:

```json
{
    ...automationConfig,
  "requiredResources": ["executionTime", "emailEnrichment"]
}
```

### Customizing Credit Consumption:

To deduct more than the default 1 credit, you can use the `"resourceConsumption"` field to specify the number of credits to be consumed for each email enrichment execution.

```json
{
    ...automationConfig,
  "requiredResources": ["executionTime", "emailEnrichment"],
  "resourceConsumption": {
    "emailEnrichment": 2
  }
}
```

## Email Credit Deduction in Automation Engine

### Summary

Prathmesh and I discussed that we will send two keys from the Automation Engine:

1. **emailFound**: `boolean` — true if an email is found, else false.
2. **dataEnrichmentCreditsToDeduct**: `number` — the total number of credits to deduct.

### Current Response:

Currently, the response from the Automation Engine looks like this:

```json
{
    "success": true,
    "response": { ... }
}
```

### Updated Response:

We will also send two new keys in the response:

```json
{
    "success": true,
    "emailFound": boolean, // true if an email is found, else false
    "dataEnrichmentCreditsToDeduct": number,  // total credits to deduct
    "response": { ... }
}
```

### Calculation of `dataEnrichmentCreditsToDeduct`:

The `dataEnrichmentCreditsToDeduct` will be calculated by multiplying the number of emails found with the `emailEnrichment` value in `resourceConsumption`. The default value for `emailEnrichment` will be 1 if not specified.

```json
dataEnrichmentCreditsToDeduct = resourceConsumption.emailEnrichment(default: 1) * number of emails found
```

## Two Use Cases:

### 1. Single Input Case:

For automations like LinkedIn Profile Scraper, we get one object as output. The calculation is straightforward:

- Check if an email is found.
- If an email is found, the response will look like this:

```json
{
  "success": true,
  "emailFound": true,
  "dataEnrichmentCreditsToDeduct": 1 // 1 is default or value of resourceConsumption.emailEnrichment
}
```

### 2. Multiple Input Case:

For automations like LinkedIn People Search Export, we get an array of objects. Here, we will:

- Check each object for an email.
- Calculate the total number of emails found.
- Multiply the number of emails found by the email consumption value (default: 1).

```json
{
    "success": true,
    "emailFound": true,
    "dataEnrichmentCreditsToDeduct": number of emails found * 1 (or resourceConsumption.emailEnrichment)
}
```
