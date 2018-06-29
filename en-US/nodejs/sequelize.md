---
name: Sequelize
---

# Perform a single transaction for 1+ queries

```
await sequelize.transaction({
    // Configure your transaction rules here. 
    deferrable: sequelize.Deferrable.SET_DEFERRED // If I do not include this, postgres will throw a fit with foreign key constraints even if I insert data into the database in the correct order to satisfy constraints. 
}, (transaction: Object): Promise<Array<Object>> => {
    var promises: Array<Promise<any>> = []
    promises.push(User.create({
        email: "you@example.com"
    }, {transaction: transaction})) // Pass the transaction object into each of your operations in this block. 
    })
    return Promise.all(promises)
})
```

You configure rules for the transaction in the transaction constructor and then pass that transaction into each operation in the below section. 

# Perform 2+ insert queries safely

To safely perform many insert statements, make sure to run them under a transaction. 

```
return sequelize.transaction({
    deferrable: sequelize.Deferrable.SET_DEFERRED
}, (transaction) => {
    var queries: Array<Promise<Object>> = []
    for (var i = 0; i < numberOfCodes; i++) {
        queries.push(
          NameModel.create({
            property_here: value_here
          }, {
            transaction: transaction
          })
        )
      }
      return Promise.all(queries)
    })
}
```