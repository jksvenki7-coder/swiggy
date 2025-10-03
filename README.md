# swiggy<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Food Delivery with WhatsApp and Map</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background: #f8f8f8;
        }
        header {
            background-color: #008cff;
            padding: 10px 20px;
            color: white;
            text-align: center;
        }
        .search-box {
            margin: 20px auto;
            max-width: 600px;
            display: flex;
        }
        .search-box input {
            flex: 1;
            padding: 10px;
            font-size: 18px;
            border: 2px solid #008cff;
            border-right: none;
            border-radius: 5px 0 0 5px;
        }
        .search-box button {
            padding: 10px 20px;
            background-color: #008cff;
            color: white;
            border: 2px solid #008cff;
            border-radius: 0 5px 5px 0;
            cursor: pointer;
            font-size: 18px;
        }
        .menu {
            max-width: 900px;
            margin: 0 auto 40px auto;
            display: grid;
            grid-template-columns: repeat(auto-fill,minmax(300px,1fr));
            gap: 15px;
            padding: 0 10px;
        }
        .card {
            background: white;
            border-radius: 8px;
            padding: 15px;
            box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
            transition: box-shadow 0.3s ease;
        }
        .card:hover {
            box-shadow: 0 4px 12px rgb(0 0 0 / 0.2);
        }
        .card img {
            width: 100%;
            border-radius: 8px;
            object-fit: cover;
            max-height: 180px;
        }
        .card h3 {
            margin: 10px 0 5px 0;
            color: #333;
        }
        .card p {
            color: #666;
        }
        .order-btn {
            display: inline-block;
            margin-top: 10px;
            padding: 10px 15px;
            background-color: #25d366;
            color: white;
            text-decoration: none;
            border-radius: 5px;
            font-weight: bold;
        }
        #map {
            max-width: 900px;
            height: 400px;
            margin: 30px auto;
            border-radius: 8px;
            box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
        }
    </style>
</head>
<body>
    <header>
        <h1>Food Delivery with WhatsApp & Map</h1>
    </header>

    <div class="search-box">
        <input type="text" id="searchInput" placeholder="Search restaurants or food..." onkeyup="filterMenu()" />
        <button onclick="filterMenu()">Search</button>
    </div>

    <div class="menu" id="menuContainer">
        <div class="card" data-name="Pizza Palace" data-address="123 Pizza Road, City" data-lat="17.385044" data-lng="78.486671">
            <img src="https://via.placeholder.com/300x180?text=Pizza+Palace" alt="Pizza Palace" />
            <h3>Pizza Palace</h3>
            <p>Delicious handmade pizzas with fresh ingredients.</p>
            <a href="https://wa.me/918977143043?text=I%20want%20to%20order%20from%20Pizza%20Palace" target="_blank" class="order-btn">Order on WhatsApp</a>
        </div>
        <div class="card" data-name="Burger Hub" data-address="45 Burger Avenue, City" data-lat="17.4500" data-lng="78.5000">
            <img src="https://via.placeholder.com/300x180?text=Burger+Hub" alt="Burger Hub" />
            <h3>Burger Hub</h3>
            <p>Tasty burgers with various toppings to choose.</p>
            <a href="https://wa.me/918977143043?text=I%20want%20to%20order%20from%20Burger%20Hub" target="_blank" class="order-btn">Order on WhatsApp</a>
        </div>
        <div class="card" data-name="Sushi Central" data-address="9 Sushi Street, City" data-lat="17.4310" data-lng="78.4450">
            <img src="https://via.placeholder.com/300x180?text=Sushi+Central" alt="Sushi Central" />
            <h3>Sushi Central</h3>
            <p>Fresh sushi and sashimi prepared by expert chefs.</p>
            <a href="https://wa.me/918977143043?text=I%20want%20to%20order%20from%20Sushi%20Central" target="_blank" class="order-btn">Order on WhatsApp</a>
        </div>
        <div class="card" data-name="Vegan Delight" data-address="77 Green Road, City" data-lat="17.4102" data-lng="78.4800">
            <img src="https://via.placeholder.com/300x180?text=Vegan+Delight" alt="Vegan Delight" />
            <h3>Vegan Delight</h3>
            <p>Healthy and delicious vegan meals to enjoy.</p>
            <a href="https://wa.me/918977143043?text=I%20want%20to%20order%20from%20Vegan%20Delight" target="_blank" class="order-btn">Order on WhatsApp</a>
        </div>
    </div>

    <div id="map"></div>

    <script>
        // Function to filter menu cards on search
        function filterMenu() {
            const input = document.getElementById('searchInput').value.toLowerCase();
            const cards = document.querySelectorAll('.menu .card');
            cards.forEach(card => {
                const name = card.getAttribute('data-name').toLowerCase();
                card.style.display = name.includes(input) ? '' : 'none';
            });
            updateMapMarkers();
        }
        
        // Google Maps with markers for each visible card
        let map;
        let markers = [];

        function loadMap() {
            const mapCenter = { lat: 17.385044, lng: 78.486671 };
            map = new google.maps.Map(document.getElementById('map'), {
                center: mapCenter,
                zoom: 12,
            });
            updateMapMarkers();
        }

        function updateMapMarkers() {
            // Remove existing markers
            markers.forEach(marker => marker.setMap(null));
            markers = [];

            const visibleCards = [...document.querySelectorAll('.menu .card')]
                .filter(card => card.style.display !== 'none');

            visibleCards.forEach(card => {
                const lat = parseFloat(card.getAttribute('data-lat'));
                const lng = parseFloat(card.getAttribute('data-lng'));
                const name = card.getAttribute('data-name');
                const addr = card.getAttribute('data-address');

                const marker = new google.maps.Marker({
                    position: { lat, lng },
                    map: map,
                    title: name,
                });

                const infoWindow = new google.maps.InfoWindow({
                    content: `<strong>${name}</strong><br/>${addr}`
                });

                marker.addListener('click', () => {
                    infoWindow.open(map, marker);
                });

                markers.push(marker);
            });
        }
    </script>
    <script async defer src="https://maps.googleapis.com/maps/api/js?key=YOUR_GOOGLE_MAPS_API_KEY&callback=loadMap"></script>
</body>
</html>
