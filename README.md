## Creating-Derived-table-with-LookML-GSP935
⚠️ Disclaimer
This script and guide are provided for the educational purposes to help you understand the lab services and boost your career. Before using the script, please open and review it to familiarize yourself with Google Cloud services. Ensure that you follow 'Qwiklabs' terms of service and YouTube’s community guidelines. The goal is to enhance your learning experience, not to bypass it.
©Credit
DM for credit or removal request (no copyright intended) ©All rights and credits for the original content belong to Google Cloud Google Cloud Skill Boost website 🙏
#🙏
#🚨 First, click the toggle button to turn on the Development mode.
![400387338-1a2d8b5f-1a0d-4659-b846-5b56e679a1a9](https://github.com/user-attachments/assets/46a461f2-b77b-4017-beb2-55345d19cb02)
#Go to Develop > qwiklabs-ecommerce LookML project.
🚨 Create view file:
 Looker Lab: Qwiklabs E-Commerce Project
This project demonstrates how to create derived views in Looker using order_details and order_details_summary, and how to update the model file accordingly.

🧾 Files & Code
🔹 1. order_details.view.lkml
lookml
Copy
Edit
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
🔹 2. order_details_summary.view.lkml
✅ Make sure to replace the view name add_a_unique_name_1718592811 with something meaningful like order_details_summary

lookml
Copy
Edit
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
    description: ""
    type: number
  }

  dimension: user_id {
    description: ""
    type: number
  }

  dimension: order_count {
    description: ""
    type: number
  }

  dimension: total_revenue {
    description: ""
    value_format: "$#,##0.00"
    type: number
  }
}
🔹 3. training_ecommerce.model.lkml
lookml
Copy
Edit
connection: "bigquery_public_data_looker"

include: "/views/*.view"
include: "/z_tests/*.lkml"
include: "/**/*.dashboard"

datagroup: training_ecommerce_default_datagroup {
  # sql_trigger: SELECT MAX(id) FROM etl_log;;
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
