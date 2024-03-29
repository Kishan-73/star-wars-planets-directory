<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Star Wars Planets</title>
    <style>
        body {
            font-family: 'Arial';
            margin: 0;
            padding: 0;
        }

        header {
            background-color:#060606;
            color: #ffffff;
            padding: 20px;
            text-align: center;
            position: fixed;
            width: 100%;
            height: 10vh;
            right: 0;
            top: 0;
            z-index: 1000;
        }

        h1 {
            margin: 0;
        }

        .container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            margin-top: 80px;
            padding: 20px;
        }

        .planets-list {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            padding: 20px;
        }
        
        .planet-card {
            background-color:transparent;
            border-radius: 10px;
            margin: 10px;
            padding: 20px;
            width: 250px;
            color: blanchedalmond;
        }
        

        .planet-card:hover {
            transform: translateY(-15px);
            background-color: rgb(175, 208, 235);
            color: black;
        }
        

        .pagination {
            margin-top: 20px;
            text-align: center;
        }

        button {
            border: 2px solid green;
            color: #59d436;
            background-color: black;
            padding: 10px 20px;
            border-radius: 17px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        button:hover {
            background-color: rgb(57, 198, 62);
            color: rgb(226, 226, 226);
        }
        body, html {
        margin: 0;
        padding: 0;
        height: 100%;
        }

        .background-video {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        object-fit: cover;
        z-index: -1;
        }

        .content {
        position: relative;
        z-index: 1;
        color: #fff; 
        text-align: center; 
        padding: 20px; 
        }
        .search-container {
        text-align: center;
        margin-bottom: 20px;
        }

        .search-bar {
        padding: 10px;
        border-radius: 5px;
        border: 1px solid #ccc;
        width: 40%;
        max-width: 210px;
        position: relative;
        left: 600px;
        bottom: 30px;
        border: 3px solid gold;
        }
        .dropdown {
            position: relative;
            display: inline-block;
            margin: 20px;
            position: relative;
            right: 670px;
            bottom: 110px;
        }

        .dropdown-content {
            display: none;
            position: absolute;
            background-color:transparent;
            min-width: 100px;
            box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
            z-index: 1;
            border-radius: 20px;
            color: whitesmoke;
        }

        .dropdown-content a {
            color: rgb(255, 251, 251);
            padding: 12px 16px;
            text-decoration: none;
            display: block;
        }

        .dropdown-content a:hover {
            background-color: #e9e7e7;
            border-radius: 17px;
            color: #060606;
        }

        .dropdown:hover .dropdown-content {
            display: block;
        }
        #globe{
            height: 35px;
            width: 35px;
            background-color:white;
            border-radius: 100px;
            position: relative;
            right: 20px;
            top: 8px;
        }

    </style>
</head>
<body>
    <video autoplay muted loop class="background-video">
        <source src="./hoth-ice-planet-star-wars-moewalls-com.mp4" type="video/mp4">
    </video>
    <div class="content">
        <header>
            <h1><img src="./globe-americas.svg" alt="" id="globe">Star Wars Planets Directory</h1>
            <div class="search-container">
               <input type="text" class="search-bar" placeholder="Search for a planet..." oninput="filterPlanets(this.value)">
            </div>
            <div class="dropdown">
                <button class="dropbtn">Menu &#9662;</button>
                <div class="dropdown-content" id="planetDropdown">
                </div>
            </div>
            </header>
            <div class="container" id="planets-list">
            </div>
            <div class="pagination" id="pagination">
            </div>
    </div>
   

    <script>
        let allPlanets = [];
        let currentPage = 1;

        async function fetchPlanets(url) {
            const response = await fetch(url);
            const data = await response.json();
            return data;
        }

        async function fetchAllPlanets() {
            let url = 'https://swapi.dev/api/planets/';
            while (url) {
                const data = await fetchPlanets(url);
                allPlanets = [...allPlanets, ...data.results];
                url = data.next;
            }
        }

        async function displayPlanets(page) {
            const startIndex = (page - 1) * 10;
            const endIndex = startIndex + 10;
            const planetsToShow = allPlanets.slice(startIndex, endIndex);
            const planetsList = document.getElementById('planets-list');
            planetsList.innerHTML = '';
            planetsToShow.forEach(planet => {
                const planetCard = document.createElement('div');
                planetCard.classList.add('planet-card');
                planetCard.innerHTML = `
                    <h2>${planet.name}</h2>
                    <p><strong>Climate:</strong> ${planet.climate}</p>
                    <p><strong>Population:</strong> ${planet.population}</p>
                    <p><strong>Terrain:</strong> ${planet.terrain}</p>
                `;
                planetsList.appendChild(planetCard);
            });
        }

        async function displayPagination() {
            const totalPages = Math.ceil(allPlanets.length / 10);
            const pagination = document.getElementById('pagination');
            pagination.innerHTML = '';
            for (let i = 1; i <= totalPages; i++) {
                const button = document.createElement('button');
                button.textContent = i;
                button.addEventListener('click', () => {
                    currentPage = i;
                    displayPlanets(currentPage);
                });
                pagination.appendChild(button);
            }
        }
        

        async function populateDropdown() {
        const planetDropdown = document.getElementById('planetDropdown');
        const data = await fetchPlanets('https://swapi.dev/api/planets/');
        const planets = data.results;
        planets.forEach(planet => {
        const link = document.createElement('a');
        link.textContent = planet.name;
        link.href = '#'; // Set the href attribute to '#' to prevent navigation
        link.addEventListener('click', (event) => {
            event.preventDefault(); // Prevent default anchor behavior
            fetchPlanetDetails(planet.name);
        });
        planetDropdown.appendChild(link);
    });
}
populateDropdown();


        fetchPlanets('https://swapi.dev/api/planets/')
            .then(data => {
                displayPlanets(data.results);
                displayPagination(data.next);
            })
            .catch(error => console.error('Error fetching planets:', error));
            
            async function filterPlanets(searchQuery) {
            const filteredPlanets = allPlanets.filter(planet => planet.name.toLowerCase().includes(searchQuery.toLowerCase()));
            const planetsList = document.getElementById('planets-list');
            planetsList.innerHTML = '';
            filteredPlanets.forEach(planet => {
                const planetCard = document.createElement('div');
                planetCard.classList.add('planet-card');
                planetCard.innerHTML = `
                    <h2>${planet.name}</h2>
                    <p><strong>Climate:</strong> ${planet.climate}</p>
                    <p><strong>Population:</strong> ${planet.population}</p>
                    <p><strong>Terrain:</strong> ${planet.terrain}</p>
                `;
                planetsList.appendChild(planetCard);
            });
        }
      

            async function init() {
            await fetchAllPlanets();
            await displayPlanets(currentPage);
            await displayPagination();
        }init();
            
    </script>
</body>
</html>
