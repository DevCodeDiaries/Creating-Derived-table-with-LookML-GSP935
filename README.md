# 🧠 Creating Derived Tables in LookML(GSP935)

This project demonstrates how to create **derived views using LookML** in Looker. You'll also learn to work with `order_details`, `order_details_summary`, and update your `model.lkml` accordingly.

---

### ⚠️ Disclaimer

This guide is for **educational purposes only**. It is created to help learners understand Looker & GCP services better. Please make sure to follow **Qwiklabs Terms of Service** and **YouTube's Community Guidelines**.  
**All content and platform credits go to Google Cloud.**

---

## 🛠️ Steps to Complete the Lab

---

### 🔧 1. Turn ON Development Mode  
Click the **toggle button** in Looker to enter development mode.

---

### 📁 2. Go to `Develop > qwiklabs-ecommerce` LookML Project

---

### 🧾 3. Create a View File: `order_details`

Replace default code with:

```lookml
view: order_details {
  derived_table: {
    sql: SELECT
        order_items.order_id AS order_id
        ,order_items.user_id AS user_id
        ,COUNT(*) AS order_item_count
        ,SUM(order_items.sale_price) AS order_revenue
      FROM cloud-training-demos.looker_ecomm.order_items
      GROUP BY order_id, user_id
       ;;
  }

  measure: count {
    hidden: yes
    type: count
    drill_fields: [detail*]
  }

  dimension: order_id {
    primary_key: yes
    type: number
    sql: ${TABLE}.order_id ;;
  }

  dimension: user_id {
    type: number
    sql: ${TABLE}.user_id ;;
  }

  dimension: order_item_count {
    type: number
    sql: ${TABLE}.order_item_count ;;
  }

  dimension: order_revenue {
    type: number
    sql: ${TABLE}.order_revenue ;;
  }

  set: detail {
    fields: [order_id, user_id, order_item_count, order_revenue]
  }
}
---


---
## 🎉 You're All Set!
You’ve successfully added the derived view and updated the model for the GSP935 LookML Lab. Great job! 💪

👩‍💻 Maintained by: DevCode Diaries
📢 Want more lab guides?
⭐ Star this repo
📬 DM for suggestions or contributions



