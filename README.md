# GUARDEVERY
"GUARDEVERY is a innovative solution developed using Visual Studio 2022 and Python to tackle the common problem of incorrect deliveries."

from flask import Flask, request, jsonify

app = Flask(__name__)

# Dummy Data for Orders and Deliveries with attributes like depth and size
orders = {
    "12345": {"product": "diya", "size": "medium", "color": "red", "depth": "deep"},
    "12346": {"product": "laptop", "size": "large", "color": "black"}
}

deliveries = {
    "12345": {"product": "diya", "size": "medium", "color": "red", "depth": "shallow"},  # Delivery mismatch for diya
    "12346": {"product": "laptop", "size": "large", "color": "black"}
}

# Function to check for discrepancies in delivery based on attributes
def check_delivery(order_id):
    order_details = orders.get(order_id, {})
    delivery_details = deliveries.get(order_id, {})

    # If order ID is invalid
    if not order_details:
        return {"status": "error", "message": "Order ID not found."}

    discrepancies = []
    
    # Check for product attributes discrepancies (like size, color, depth)
    for attribute, value in order_details.items():
        if attribute == "product":  # Skip the product name comparison
            continue
        
        if value != delivery_details.get(attribute, value):
            discrepancies.append(f"{attribute.capitalize()} mismatch: Ordered {value}, Delivered {delivery_details.get(attribute)}")

    # Create the final response based on discrepancies
    if not discrepancies:
        return {"status": "success", "message": "Delivery matches the order."}
    else:
        return {
            "status": "discrepancy",
            "message": "There are discrepancies in the delivery.",
            "details": discrepancies
        }

@app.route('/check_delivery', methods=['POST'])
def check_delivery_api():
    data = request.get_json()
    order_id = data.get('order_id')

    # Validate input
    if not order_id:
        return jsonify({"status": "error", "message": "Order ID is required."})

    result = check_delivery(order_id)
    return jsonify(result)

if __name__ == '__main__':
    app.run(debug=True)
