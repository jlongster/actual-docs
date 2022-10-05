import { Method, MethodBox } from './types';

The API gives you full programmatic access to your data. If you are a developer, you can use this to import transactions from a custom source, export data to another app like Excel, or write anything you want on top of Actual.

One thing to keep in mind: Actual is not like most other apps. All of your data already exists locally, so using the API does not mean you'll be contacting our servers. You'll simply be accessing your local data.

Currently, **the API requires Actual to be running locally** because of this. In the future, the client will contain all the code necessary to query your data and will work by itself. Right now the primary use case is custom importers and exporters, so running Actual first is natural.

## Getting started

We provide an official node.js client in the `@actual-app/api` package. Other languages are not supported at this point.

The client is [full open-source on Github](https://github.com/actualbudget/node-api) if you want to see the code.

Install it with either `npm` or `yarn`:

```
npm install --save @actual-app/api
```

```
yarn add @actual-app/api
```

The simplest way to get started is with `runWithBudget`. This will do all the connection setup and teardown for you. It takes the `id` of the budget to use and a function to run.

This is all the code to get the budget values for the current month:

```js
let api = require('@actual-app/api');

async function run() {
  let budget = await api.getBudgetMonth('2019-10');
  console.log(budget);
}

api.runWithBudget('My-Budget', run);
```

Remember, **before running scripts you must start Actual**. This code will connect to Actual, select the budget `My-Budget` and print out the result of [`getBudgetMonth`](/docs/developers/API/#getbudgetmonth). Read the [reference docs](/docs/developers/API/) to see all the methods available.

You can find your budget id in the "Advanced" section of the settings page.

If you want, you can use some low-level methods (see below) to manage the connection yourself, but this is not recommended.

## Writing data importers

If you are using another app, like YNAB or Mint, you might want to migrate your data. Right now only officially support [importing YNAB4 data](/docs/overview/migrating-from-other-apps/) (and it works very well). But if you want to import all of your data into Actual, you can write a custom importer.

Note that this is not about importing transactions. If all you want to do is add transactions from a custom source (like your banks API), use [`importTransactions`](/docs/developers/API/#importtransactions). In this context, a custom importer is something takes _all_ of your data (budgets, transactions, payees, etc) and dumps them all into a new file in Actual.

The API has a special mode for bulk importing data. In this mode, a new file is always created (you can't bulk import into an existing file), and it will run much faster than if you did it normally.

To write a custom importer, use `runImport` instead of `runWithBudget`. `runImport` takes the _name_ of the file you want to create and runs a function. Here is an example importer:

```js
let api = require('@actual-app/api');
let data = require('my-data.json');

async function run() {
  for (let account of data.accounts) {
    let acctId = await api.createAccount(convertAccount(account));
    await api.addTransactions(
      acctId,
      data.transactions
        .filter(t => t.acctId === acctId)
        .map(convertTransaction)
    );
  }
}

api.runImport('My-Budget', run);
```

This is very simple, but it takes some data in `my-data.json` and creates all the accounts and transactions from it. Functions to convert the items (like `convertAccount`) are not included here. Use the [reference docs](/docs/developers/API/) to learn the shape of objects that Actual expects.

**Note:** it's important that [`addTransactions`](/docs/developers/API/#addtransactions) is used here. You want to use it instead of [`importTransactions`](/docs/developers/API/#importtransactions) when dumping raw data into Actual. The former will not run the reconciliation process (which dedupes transactions), and won't create the other side of transfer transactions, and more. If you use `importTransactions` it may adjust your data in ways that don't match the data your importing.

If you want to see an example of a real importer, our YNAB4 importer is [completely open-source](https://github.com/actualbudget/import-ynab4/blob/master/importer.js). It's built on exactly the same API that you are using.

## Methods

These are the two public methods that you can use. The API also exports low-level functions like `init`, `send`, `disconnect`, and `loadBudget` if you want to manually manage the connection. You can [read the source](https://github.com/actualbudget/node-api/blob/master/connection.js) to learn about those methods.

#### `runWithBudget`

<Method name="runWithBudget" args={[{ name: 'budgetId', type: 'string'}, { name: 'func', type: 'function' }]} returns="Promise<null>" />

After connecting to the budget `budgetId`, run the function. This function can assume all API methods are ready to use.

You can find your budget id in the "Advanced" section of the settings page.

#### `runImport`

<Method name="runImport" args={[{ name: 'budgetName', type: 'string'}, { name: 'func', type: 'function' }]} returns="Promise<null>" />

Create the budget `budgetName`, connect to it, and run the function. This puts the API in a special "import mode" that does some maintenance work to create a new budget, and bulk importing data runs much faster.