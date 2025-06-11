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
## 🔧 Step 4: Create View File - `order_details_summary`

Create a new view file named:

Paste the following code:

```lookml
view: order_details_summary {
  derived_table: {
    explore_source: order_items {
      column: order_id {}
      column: user_id {}
      column: order_count {}
      column: total_revenue {}
    }
  }

  dimension: order_id {
    type: number
  }

  dimension: user_id {
    type: number
  }

  dimension: order_count {
    type: number
  }

  dimension: total_revenue {
    value_format: "$#,##0.00"
    type: number
  }
}

🧾 Step 5: Update the training_ecommerce.model.lkml File
Paste the following code inside your model file:

lookml
Copy code
connection: "bigquery_public_data_looker"

include: "/views/*.view"
include: "/z_tests/*.lkml"
include: "/**/*.dashboard"

datagroup: training_ecommerce_default_datagroup {
  max_cache_age: "1 hour"
}

persist_with: training_ecommerce_default_datagroup

label: "E-Commerce Training"

explore: order_items {
  join: order_details {
    type: left_outer
    sql_on: ${order_items.order_id} = ${order_details.order_id};;
    relationship: many_to_one
  }

  join: users {
    type: left_outer
    sql_on: ${order_items.user_id} = ${users.id} ;;
    relationship: many_to_one
  }

  join: inventory_items {
    type: left_outer
    sql_on: ${order_items.inventory_item_id} = ${inventory_items.id} ;;
    relationship: many_to_one
  }

  join: products {
    type: left_outer
    sql_on: ${inventory_items.product_id} = ${products.id} ;;
    relationship: many_to_one
  }

  join: distribution_centers {
    type: left_outer
    sql_on: ${products.distribution_center_id} = ${distribution_centers.id} ;;
    relationship: many_to_one
  }
}

explore: events {
  join: event_session_facts {
    type: left_outer
    sql_on: ${events.session_id} = ${event_session_facts.session_id} ;;
    relationship: many_to_one
  }

  join: event_session_funnel {
    type: left_outer
    sql_on: ${events.session_id} = ${event_session_funnel.session_id} ;;
    relationship: many_to_one
  }

  join: users {
    type: left_outer
    sql_on: ${events.user_id} = ${users.id} ;;
    relationship: many_to_one
  }
}

---
## 🎉 You're All Set!
You’ve successfully added the derived view and updated the model for the GSP935 LookML Lab. Great job! 💪

👩‍💻 Maintained by: DevCode Diaries
📢 Want more lab guides?
⭐ Star this repo
📬 DM for suggestions or contributions



