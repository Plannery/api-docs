---
title: Plannery API v1

language_tabs:
  - shell

toc_footers:
 - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>
---

# Introduction

This is the API for the Plannery app. It provides methods to create and update user accounts, add linked plaid accounts and return financial plan data.

JSON is the only supported request and response format.

# Authentication

```shell
# With curl, supply the credentials with the -u option
curl "https://prod.planneryapp.com/api/plans/current"
  -u "todd@packer.com:auth-token"
```

Some API methods require an authentication token, which is returned from the login and register methods. Tokens should be considered valid indefinitely, but can expire at any time.

The API expects credentials via HTTP Basic Authentication, with the user's email address as the username and the authentication token as the password.

# Versioning

```shell
Accept: application/vnd.plannery.v1+json
```

Use the Accept HTTP header to indicate the requested version of the API. At this time, the api will default to the most recent version. You should always specify the requested version.

# Accounts

## Create a new user account

```shell
# EXAMPLE REQUEST

{
  "first_name": "Todd",
  "last_name": "Packer",
  "email": "todd@packer.com",
  "password": "letmein",
  "phone_number": "555-555-1234",
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1,
  "authentication_token": "abcd1234",
  "first_name": "Todd",
  "last_name": "Packer",
  "email": "todd@packer.com",
  "phone_number": "555-555-1234"
}
```

This endpoint creates a new user account.

### HTTP Request

`POST https://prod.planneryapp.com/api/user`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
email     | true | The user's email address.
password | true | The user's password.
first_name | true | The user's first name.
last_name | true | The user's last name.
phone_number | true | The user's phone number. Should be nine digits. Formatting is stripped when saved.

## Login to existing user account

```shell
# EXAMPLE REQUEST

{
  "email": "todd@packer.com",
  "password": "letmein"
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1,
  "authentication_token": "abcd1234",
  "first_name": "Todd",
  "last_name": "Packer",
  "email": "todd@packer.com",
  "phone_number": "555-555-1234",
}
```

This endpoint logs in to an existing user account. Each login will return a new authentication token, and the old authentication token will be invalidated.

### HTTP Request

`POST https://prod.planneryapp.com/api/user/login`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
email     | true | The user's email address.
password | true | The user's password.

# PlaidItem

## Create a new plaid item

```shell
# EXAMPLE REQUEST

{
  "public_token": "public-token-sandbox-1234556",
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1
}
```

This endpoint creates a new plaid_item instance then asynchronously loads all the accounts and transactions from plaid.

### HTTP Request

`POST https://prod.planneryapp.com/api/plaid_items`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
public_token     | true | This token should be retrieved from plaid link.

## Get plaid_item linking status

```shell
# EXAMPLE REQUEST

{
  "id": "1"
}
```

```shell
# EXAMPLE RESPONSE IF ERROR

{
  "title": "Account Failed to Link"
  "message": "Please try again."
}

# EXAMPLE RESPONSE IF PENDING

{
  "id": "1"
}

# EXAMPLE RESPONSE IF SUCCESSFUL

{"id": 4,
 "plaid_item_identifier": "QbMJmZp94lfmJ7bEWEmmt7MkvPkXePtpMrzV1",
 "plaid_institution_identifier": "ins_109508",
 "available_products":
  ["assets", "auth", "balance", "credit_details", "identity"],
 "plaid_accounts":
 [{"id": 26,
    "available_balance_in_dollars": 100.0,
    "current_balance_in_dollars": 110.0,
    "limit_balance_in_dollars": 0.0,
    "mask": "0000",
    "name": "Plaid Checking",
    "official_name": "Plaid Gold Standard 0% Interest Checking",
    "account_subtype": "checking",
    "account_type": "depository",
    "interest_rate": 0.0},
   {"id": 27,
    "available_balance_in_dollars": 200.0,
    "current_balance_in_dollars": 210.0,
    "limit_balance_in_dollars": 0.0,
    "mask": "1111",
    "name": "Plaid Saving",
    "official_name": "Plaid Silver Standard 0.1% Interest Saving",
    "account_subtype": "savings",
    "account_type": "depository",
    "interest_rate": 0.1}]}]
```

This endpoint can be used to know when the linking phases has completed.

### HTTP Request

`GET  https://prod.planneryapp.com/api/plaid_items/:id/status`

### Request Parameters

none

## Get all plaid_items

```shell
# EXAMPLE RESPONSE

{"id": 4,
 "plaid_item_identifier": "QbMJmZp94lfmJ7bEWEmmt7MkvPkXePtpMrzV1",
 "plaid_institution_identifier": "ins_109508",
 "available_products":
  ["assets", "auth", "balance", "credit_details", "identity"],
 "plaid_accounts":
 [{"id": 26,
    "available_balance_in_dollars": 100.0,
    "current_balance_in_dollars": 110.0,
    "limit_balance_in_dollars": 0.0,
    "mask": "0000",
    "name": "Plaid Checking",
    "official_name": "Plaid Gold Standard 0% Interest Checking",
    "account_subtype": "checking",
    "account_type": "depository",
    "interest_rate": 0.0},
   {"id": 27,
    "available_balance_in_dollars": 200.0,
    "current_balance_in_dollars": 210.0,
    "limit_balance_in_dollars": 0.0,
    "mask": "1111",
    "name": "Plaid Saving",
    "official_name": "Plaid Silver Standard 0.1% Interest Saving",
    "account_subtype": "savings",
    "account_type": "depository",
    "interest_rate": 0.1},
   {"id": 28,
    "available_balance_in_dollars": 0.0,
    "current_balance_in_dollars": 1000.0,
    "limit_balance_in_dollars": 0.0,
    "mask": "2222",
    "name": "Plaid CD",
    "official_name": "Plaid Bronze Standard 0.2% Interest CD",
    "account_subtype": "cd",
    "account_type": "depository",
    "interest_rate": 0.2},
   {"id": 29,
    "available_balance_in_dollars": 0.0,
    "current_balance_in_dollars": 410.0,
    "limit_balance_in_dollars": 2000.0,
    "mask": "3333",
    "name": "Plaid Credit Card",
    "official_name": "Plaid Diamond 12.5% APR Interest Credit Card",
    "account_subtype": "credit card",
    "account_type": "credit",
    "interest_rate": 12.5},
   {"id": 30,
    "available_balance_in_dollars": 43200.0,
    "current_balance_in_dollars": 43200.0,
    "limit_balance_in_dollars": 0.0,
    "mask": "4444",
    "name": "Plaid Money Market",
    "official_name": "Plaid Platinum Standard 1.85% Interest Money Market",
    "account_subtype": "money market",
    "account_type": "depository",
    "interest_rate": 1.85}]}]
```

This endpoint retrieves all users linked plaid_items (which includes the plaid_accounts)

### HTTP Request

`GET  https://prod.planneryapp.com/api/plaid_items`

### Request Parameters

none

# Plan

## Create a financial plan

```shell
# EXAMPLE REQUEST

{
  "goal" "emergency_fund"
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 2,
  "goal": "emergency_fund",
 "generated_at": "Thu, 29 Nov 2018 01:40:33 UTC +00:00",
 "plan_financial_profile": {
    "id": 2,
    "total_assets_in_dollars": 42531.76,
    "total_liabilities_in_dollars": 4910.0,
    "monthly_cash_flow_in_dollars": -3465.26,
    "average_liability_apr": 0.0
 },
 "plan_instructions": [
    {
      "id": 4,
      "instruction_type": "open_personal_loan",
      "title": "consolidate debt",
      "details": "Your monthly payment will go up by $155.00 but you'll pay less interest on your debt",
      "number_of_months": nil,
      "net_worth_increase_in_cents": 51488,
      "net_worth_decrease_explanation": nil,
      "completed": false,
      "instruction_items":
      [{
        "section_title": "Consolidate Existing Loans",
        "section_items": ["Move $4,500.00 with 22.0% APR into the new loan"]}],
      "recommended_financial_product": {
        "id": 1,
        "institution_name": "Upstart",
        "annual_percentage_rate": 15.0,
        "min_amount_in_cents": 100000,
        "max_amount_in_cents":2000000,
        "sign_up_url": nil,
        "logo_url": "http://prod.planneryapp.com/rails/active_storage/blobs/sdf--d57583e0b6f706a4eebaedf57a1e89cec2823a7c/logo_icon.jpg",
        "fees_in_cents": 0,
        "type": "PersonalLoan"
      },
      "other_financial_products": []
    },
    {
       "id": 5,
       "instruction_type": "reduce_deposits",
       "title": "pause monthly transfers of $1,000.00",
       "details": "your monthly cash flow is still negative.",
       "number_of_months": nil,
       "net_worth_increase_in_cents": 18062,
       "net_worth_decrease_explanation": nil,
       "completed": false,
       "instruction_items":
       [{
         "section_title": "Pause Transfer",
         "section_items": ["Stop depositing $1,000.00 per month into Plaid CD"]
       }],
       "recommended_financial_product": nil,
       "other_financial_products": []},
    {
       "id": 6,
       "instruction_type": "reduce_spending",
       "title": "reduce monthly expenses by $2,821.25",
       "details": "Your cash flow is still negative, Find savings by comparing your expenses to others in your area.",
       "number_of_months": nil,
       "net_worth_increase_in_cents": 3476888,
       "net_worth_decrease_explanation": nil,
       "completed": false,
       "instruction_items": [],
       "recommended_financial_product": nil,
       "other_financial_products": []
     },
   {
       "id": 7,
       "instruction_type": "open_savings",
       "title": "set up your emergency fund",
       "details": "recommended emergency fund is to cover 3 months of expenses. You can hit your goal of $3,612.03 by January 2018.",
       "number_of_months": 10,
       "net_worth_increase_in_cents": -37030,
       "net_worth_decrease_explanation": nil,
       "completed": false,
       "instruction_items":
       [{
         "section_title": "Open New Savings Account",
         "section_items":
          ["It is best to save your emergency fund in a separate account. We found an account that offers an APR of 2.1%"]},
        {"section_title": "Set Up Direct Deposit",
         "section_items": ["Set up a direct deposit of -370.0 from your paycheck or your primary checking account. "]
       }],
       "recommended_financial_product": {
         "id": 3,
         "institution_name": "American Express",
         "annual_percentage_rate": 2.1,
         "min_amount_in_cents": 500000,
         "max_amount_in_cents": 2000000,
         "sign_up_url": "https://www.amex.com/sign-me-up",
         "logo_url": "http://prod.planneryapp.com/rails/active_storage/blobs/sdf--d57583e0b6f706a4eebaedf57a1e89cec2823a7c/logo_icon.jpg",
         "fees_in_cents": 0,
         "type": "HighYieldSaving"},
       "other_financial_products": [{
         "id": 2,
         "institution_name": "Marcus",
         "annual_percentage_rate": 2.0,
         "min_amount_in_cents": 20000,
         "max_amount_in_cents": 100000,
         "sign_up_url": "https://www.marcus.com/sign-up",
         "logo_url": nil,
         "fees_in_cents": 0,
         "type": "HighYieldSaving"
        }]
    }
  ]
}
```

This endpoint creates a financial plan along with step by step instructions.

### HTTP Request

`POST https://prod.planneryapp.com/api/plans`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
goal | true | The user's financial goal (e.g. reduce_debt, emergency_fund)

## Get a user's current financial plan

```shell
# EXAMPLE RESPONSE

same response as plan creation
```

This endpoint gets the last plan created for that user.

### HTTP Request

`GET https://prod.planneryapp.com/api/plans/current`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
none

# Setting

## Retrieve settings for the clients

```shell
# EXAMPLE RESPONSE FOR AN AUTHENTICATED USER

{"authenticated": true,
 "user":
  {"id": 7,
   "email": "todd@plannery.com",
   "first_name": "Todd",
   "last_name": "Packer",
   "authentication_token": "hp1zf7Q9Z49Qw1wWy2_x",
   "user_profile": nil,
   "user_real_estates": []},
 "plaid_public_token": "access-sandbox-7cb00b31-6ba0-4d42-97ad",
 "plan_goal_options": ["reduce_debt", "emergency_fund"],
 "plan_future_goal_options":
  ["save for a home",
   "plan for retirement",
   "improve credit score",
   "plan for sabbatical",
   "save for vacation"],
 "credit_score_options":
  [[300, 550], [550, 649], [650, 699], [700, 749], [750, 850]],
 "terms_of_service_url": "https://www.example.com/terms_of_service"}

 # EXAMPLE RESPONSE FOR AN UNAUTHENTICATED USER
 {"authenticated": false,
 "user": nil,
 "plaid_public_token": "access-sandbox-7cb00b31-6ba0-4d42-97ad",
 "plan_goal_options": ["reduce_debt", "emergency_fund"],
 "plan_future_goal_options":
  ["save for a home",
   "plan for retirement",
   "improve credit score",
   "plan for sabbatical",
   "save for vacation"],
 "credit_score_options":
  [[300, 550], [550, 649], [650, 699], [700, 749], [750, 850]],
 "terms_of_service_url": "https://www.example.com/terms_of_service"}
```

This endpoint should be requested on each app load. The response includes the
user attributes and whether or not their auth token is valid. If 'authenticated' is false,
then the client should force the login screen.

### HTTP Request

`GET https://prod.planneryapp.com/api/settings`

### Request Parameters

none

# UserProfile

## Update user attributes

```shell
# EXAMPLE REQUEST

{
  "credit_score": 700,
  "zip_code": "94115",
  "gross_income_in_dollars": 80000,
  "retirement_percent_matching": 0.0
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1,
  "credit_score": 700,
  "zip_code": "94115",
  "gross_income_in_dollars": 80000,
  "retirement_percent_matching": 0.0
}
```

This endpoint allows us to save additional information about a user. These
attributes will be editable from the settings page.

### HTTP Request

`PUT https://prod.planneryapp.com/api/user_profile`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
credit_score | true | The credit_score should be the average of the range
zip_code | false | The zip code for users that rent.
gross_income_in_dollars | false | The user's gross yearly income
retirement_percent_matching | false | The 401k matching offered by employer

# UseAccounts

## Create a new user_account

```shell
# EXAMPLE REQUEST

{
  "institution_name": "Bank of America",
  "account_type": "checkings",
  "current_balance_in_dollars": 12000,
  "monthly_deposit_in_dollars": 3400,
  "monthly_living_expenses_in_dollars": 1200,
  "annual_percentage_rate": 0.2
  "primary": true
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1,
  "institution_name": "Bank of America",
  "account_type": "checkings",
  "current_balance_in_dollars": 12000,
  "monthly_deposit_in_dollars": 3400,
  "monthly_living_expenses_in_dollars": 1200,
  "annual_percentage_rate": 0.2
  "primary": true
}
```

This endpoint allows users to manually enter in their account details instead of
linking their accounts with plaid.

### HTTP Request

`POST https://prod.planneryapp.com/api/user_accounts`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
institution_name | true | The name of the financial institution
account_type | true | The account type (checkings, savings, brokerage, 401k, ira, other_asset, credit_card, loan, mortgage, home_equity_line, student_loan, other_liability)
current_balance_in_dollars | true | The current account balance
limit_balance_in_dollars | false | The credit limit
original_balance_in_dollars | false | The original amount (only used for loans)
monthly_deposit_in_dollars | false | Average monthly deposits to account
monthly_payment_in_dollars | false | Average monthly payments to account
monthly_living_expenses_in_dollars | false | Average monthly living expenses charged to account
monthly_pmi_amount_in_dollars | false | Total amount paid for private mortgage insurance
term_in_months | false | The term length in months for a loan
in_repayment | false | Boolean for whether or not a user is in a repayment plan for their student loans
in_income_repayment_plan | false | Boolean for whether or not a user is in an income repayment plan for their student loans
primary   | false | If this is their primary checking/savings account
annual_percentage_rate | true | APR for their account

# UserRealEstate

## Create a new user_real_estate instance

```shell
# EXAMPLE REQUEST

{
  "street_address": "2315 Bush Street",
  "apt_number": 6,
  "city": "San Francisco",
  "state": "CA",
  "zip": 94115
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 2,
  "street_address": "2315 Bush Street",
  "apt_number": 6,
  "city": "San Francisco",
  "state": "CA",
  "zip": 94115
  "zestimate_amount_in_dollars": 1777167
}
```

This endpoint allows users to add real estate holdings. After the user real estate
instance is created, we then ping zillow to get the current property value (i.e. Zestimate)

### HTTP Request

`POST https://prod.planneryapp.com/api/user_real_estates`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
street_address | true | The street address
apt_number | false | The apartment number
state | true | The state
zip | true | The zip code

# WaitList

## Create a new WaitList instance

```shell
# EXAMPLE REQUEST

{
  "email": "todd@packer.com",
  "goal": "save for a house"
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1
}
```

This allows users to sign up for a waitlist to be notified when we support their desired financial goal.

### HTTP Request

`POST https://prod.planneryapp.com/api/waitlist`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
email     | true | The user's email address.
goal | true | The user's desired goal.
