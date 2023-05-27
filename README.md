from flask import Flask, request, jsonify

app = Flask(__name__)

# In-memory restaurant collection
restaurants = []

# Helper function to find a restaurant by ID
def find_restaurant_by_id(restaurant_id):
    for restaurant in restaurants:
        if restaurant['id'] == restaurant_id:
            return restaurant
    return None

# API: Adding a new restaurant
@app.route('/restaurant', methods=['POST'])
def add_restaurant():
    new_restaurant = request.json
    new_restaurant['id'] = len(restaurants) + 1
    restaurants.append(new_restaurant)
    return jsonify(new_restaurant), 201

# API: Updating restaurant rating
@app.route('/restaurant/<int:restaurant_id>', methods=['PUT'])
def update_restaurant(restaurant_id):
    restaurant = find_restaurant_by_id(restaurant_id)
    if restaurant is None:
        return jsonify({'error': 'Restaurant not found'}), 404
    
    data = request.json
    restaurant['averageRating'] = data['averageRating']
    restaurant['votes'] = data['votes']
    
    return jsonify(restaurant)

# API: Getting all restaurants
@app.route('/restaurant', methods=['GET'])
def get_all_restaurants():
    return jsonify(restaurants)

# API: Getting all restaurants in a particular city
@app.route('/restaurant/query', methods=['GET'])
def get_restaurants_by_city():
    city = request.args.get('city')
    city_restaurants = [restaurant for restaurant in restaurants if restaurant['city'] == city]
    return jsonify(city_restaurants)

# API: Getting restaurant by id
@app.route('/restaurant/query', methods=['GET'])
def get_restaurant_by_id():
    restaurant_id = request.args.get('id')
    restaurant = find_restaurant_by_id(restaurant_id)
    if restaurant is None:
        return jsonify({'error': 'Restaurant not found'}), 404
    return jsonify(restaurant)

# API: Deleting restaurant by id
@app.route('/restaurant/<int:restaurant_id>', methods=['DELETE'])
def delete_restaurant(restaurant_id):
    restaurant = find_restaurant_by_id(restaurant_id)
    if restaurant is None:
        return jsonify({'error': 'Restaurant not found'}), 404
    restaurants.remove(restaurant)
    return jsonify({'message': 'Restaurant deleted'})

# API: Sort the restaurants according to rating
@app.route('/restaurant/sort', methods=['GET'])
def sort_restaurants_by_rating():
    sorted_restaurants = sorted(restaurants, key=lambda r: float(r['averageRating']), reverse=True)
    return jsonify(sorted_restaurants)

# Run the Flask app
if __name__ == '__main__':
    app.run()
