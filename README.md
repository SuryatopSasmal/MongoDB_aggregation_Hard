# MongoDB_aggregation_Hard
### Hard Difficulty

**1. Category Value and Classification:**

- **Task:** For each `category`, calculate its total inventory value (sum of `price * quantity` for all products in that category). Then, classify each category: if total value > 10000, it's "High Value"; if > 5000, it's "Medium Value"; otherwise, it's "Standard Value".
- **Output:** Display `category` (as `categoryName`), `totalInventoryValue`, and `valueClassification`.
- **Hint:** First `$group` by category to sum `price * quantity`. Then use `$project` with `$cond` (possibly nested or using `$switch`) for classification.
- **Expected Output (order might vary):**
<pre>
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      totalInventoryValue: {
        $sum: { $multiply: ["$price", "$quantity"] }
      }
    }
  },
  {
    $project: {
      _id: 0,
      categoryName: "$_id",
      totalInventoryValue: 1,
      valueClassification: {
        $switch: {
          branches: [
            {
              case: { $gt: ["$totalInventoryValue", 10000] },
              then: "High Value"
            },
            {
              case: { $gt: ["$totalInventoryValue", 5000] },
              then: "Medium Value"
            }
          ],
          default: "Standard Value"
        }
      }
    }
  }
]);
</pre>
![image](https://github.com/user-attachments/assets/651534aa-7279-4515-a51f-f4c2054c0d9c)
**2. Suppliers and Their Most Expensive Product:**

- **Task:** For each `supplier.name`, find the name and price of the most expensive product they supply.
- **Output:** Display `supplierName`, `mostExpensiveProductName`, and `maxPrice`.
- **Hint:** You might need to `$sort` first within groups or use `$max` carefully. One approach: `$sort` by price, then `$group` using `$first` to pick the top item's details for each supplier.
- **Expected Output (order might vary):**
<pre>
  db.products.aggregate([
  {
    $sort: {
      "price": -1
    }
  },
  {
    $group: {
      _id: "$supplier.name",
      mostExpensiveProductName: { $first: "$name" },
      maxPrice: { $first: "$price" }
    }
  },
  {
    $project: {
      _id: 0,
      supplierName: "$_id",
      mostExpensiveProductName: 1,
      maxPrice: 1
    }
  }
]);

</pre>
![image](https://github.com/user-attachments/assets/9ec5d87b-2e7d-468c-a4c7-5a7c389eab4f)
![image](https://github.com/user-attachments/assets/c0ad46ba-5652-4369-87ff-e5d1297e949b)

3. Products with "portable" tag but NOT "computer" tag:
*   Task: Find products that have the "portable" tag but DO NOT have the "computer" tag. Display their name and tags.
*   Hint: Use $match. You'll need to match for inclusion of one tag and exclusion of another. Consider $all and $nin or a combination of $elemMatch and $not. A simple $and with two conditions on the tags array is often effective.
  <pre>
  db.products.aggregate([
  {
    $match: {
      tags: {
        $all: ["portable"],
        $nin: ["computer"]
      }
    }
  },
  {
    $project: {
      _id: 0,
      name: 1,
      tags: 1
    }
  }
]);
</pre>
![image](https://github.com/user-attachments/assets/d5401646-d7d5-4564-b2b0-60a294d7f6df)
