import Key from '../components/Key'

## Undo/redo

If you ever make a mistake, pressing <Key mod="cmd" k="Z" /> will undo, and <Key mods={["cmd", "shift"]} k="Z" /> will redo. This is an undo system that you can rely on; any change can be undone and the UI will walk back in time.

## Keyboard shortcuts

A few global shortcuts:

* <Key mod="cmd" k="1" /> will show the budget
* <Key mod="cmd" k="2" /> will show reports
* <Key mod="cmd" k="3" /> will show all accounts
* <Key mod="cmd" k="o" /> will close the file and list other available files to open

### Budget

* <Key k="Enter" /> while editing a budget amount will move to the next category.
* <Key mod="shift" k="Enter" /> will move to the previous category.

### Transactions

* When editing, <Key k="Enter" /> and <Key mod="shift" k="Enter" /> will move down and up. If a dropdown is open and you've typed a new value, this will instead save the value and close the dropdown.
* When editing, <Key k="Tab" /> and <Key mod="shift" k="Tab" /> will move left and right
* When adding a new transaction, <Key mod="cmd" k="Enter" /> will add it regardless of where you are editing. Pressing <Key k="Enter" /> in either the Payment or Deposit columns will add it as well.


## View multiple months at once

In the top left of the budget, you will see this control:

<img src="/images/months-selector.png" width="100" />

This sets the maximum amount of months to render at once, and defaults to 1. If you want to view multiple months on the same page, click the boxes to increase the number.

*Note:* This only controls the *maximum* number of months. If the app is too small to render all of them it will only render the months that fit on the screen.

## Show running balances

A "running balance" is the balance of the account after every transaction over time. This is very useful for reconciling accounts with banks because you can see the balance at a specific date and use it to compare it with your bank.

To enable this:

1. Click on an account
2. Click on the 3 dots to show the actions menu
3. Select "Show running balance"

A new column should appear which shows the balance of the account after each transaction:

<img src="/images/running-balance.png" width="250" />


## Add a transaction from any screen on mobile

If you are manually adding transactions from a mobile device, you probably want to quickly add a transaction without clicking into an account first.

You can do that! **Simply press and hold down the "Accounts" tab** at the bottom and the "add transaction" screen will appear. The account field will be automatically pre-filled to whichever the last account a transaction was added to.