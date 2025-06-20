# movie-recommendation-app
A simple app to help people decide what movie they should watch
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Personalized Movie Recommender</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chosen Palette: "Warm Neutral Harmony" - A calming palette with a light cream background (bg-orange-50), dark slate text for readability (text-slate-800), and a gentle teal accent (teal-600) for interactive elements like buttons and highlights. This creates a supportive and non-distracting user experience. -->
    <!-- Application Structure Plan: The SPA is structured into three clear, task-oriented sections: 1) Genre Selection, 2) Recommendation Results, and 3) User's Favorites. This linear, top-to-bottom flow was chosen for its intuitive user journey. The user provides input at the top, sees the direct results in the middle, and can curate their personal collection at the bottom. This separation of concerns makes the app easy to understand and navigate without any complex navigation menus. -->
    <!-- Visualization & Content Choices: The primary "visualization" is a dynamic grid of movie cards, created with HTML and styled with Tailwind CSS. This method was chosen over charts because the goal is to present rich, item-specific information (title, image, description), not quantitative data. The core interaction is filtering via checkboxes, which is a direct and universally understood UI pattern for selection. This choice supports the project's goal of a simple, lightweight, and user-friendly tool. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        /* A few custom styles to complement Tailwind */
        body {
            font-family: 'Inter', sans-serif;
        }
        .movie-card {
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .movie-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
        }
        .favorite-btn.favorited {
            background-color: #dc2626; /* red-600 */
            color: white;
        }
    </style>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
</head>
<body class="bg-orange-50 text-slate-800">

    <div class="container mx-auto p-4 sm:p-6 lg:p-8">
        
        <header class="text-center mb-8">
            <h1 class="text-4xl sm:text-5xl font-bold text-slate-900 mb-2">Movie Recommender</h1>
            <p class="text-lg text-slate-600">Find your next favorite film based on your preferred genres!</p>
        </header>

        <main>
            <section id="genre-selection" class="bg-white p-6 rounded-xl shadow-md mb-8">
                <h2 class="text-2xl font-bold mb-4">Select Your Preferred Genres:</h2>
                <div id="genre-checkboxes" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 gap-4 mb-6">
                    <!-- Checkboxes will be dynamically generated here -->
                </div>
                <button id="get-recommendations-btn" class="w-full sm:w-auto bg-teal-600 text-white font-bold py-3 px-6 rounded-lg hover:bg-teal-700 transition-colors duration-300">
                    Get Recommendations
                </button>
            </section>

            <section id="recommendation-results" class="mb-8">
                <h2 class="text-3xl font-bold mb-6">Recommended Movies:</h2>
                <div id="movies-container" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-6">
                    <!-- Movie cards will be dynamically inserted here -->
                </div>
            </section>

            <section id="favorite-movies">
                <h2 class="text-3xl font-bold mb-6">Your Favorite Movies:</h2>
                <div id="favorites-container" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-6">
                    <!-- Saved favorite movies will be displayed here -->
                </div>
            </section>
        </main>

        <footer class="text-center mt-12 py-6 border-t border-slate-200">
            <p class="text-slate-500">&copy; Summer 2025 - Mellanie Maina</p>
        </footer>
    </div>

    <script>
    document.addEventListener('DOMContentLoaded', () => {

        // --- 1. DATA STORE ---
        // A static array of movie objects to simulate a database.
        const movies = [
            { id: "tt1375666", title: "Inception", genre: ["Sci-Fi", "Action", "Thriller"], description: "A thief who steals corporate secrets through use of dream-sharing technology...", year: 2010, director: "Christopher Nolan", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Inception" },
            { id: "tt0468569", title: "The Dark Knight", genre: ["Action", "Crime", "Drama"], description: "The menace known as the Joker wreaks havoc on Gotham City...", year: 2008, director: "Christopher Nolan", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Dark+Knight" },
            { id: "tt0137523", title: "Fight Club", genre: ["Drama", "Thriller"], description: "An insomniac office worker looking for a way to change his life crosses paths with a devil-may-care soap maker...", year: 1999, director: "David Fincher", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Fight+Club" },
            { id: "tt0110912", title: "Pulp Fiction", genre: ["Crime", "Drama"], description: "The lives of two mob hitmen, a boxer, a gangster's wife, and a pair of diner bandits intertwine...", year: 1994, director: "Quentin Tarantino", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Pulp+Fiction" },
            { id: "tt0816692", title: "Interstellar", genre: ["Sci-Fi", "Drama", "Adventure"], description: "A team of explorers travel through a wormhole in space in an attempt to ensure humanity's survival...", year: 2014, director: "Christopher Nolan", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Interstellar" },
            { id: "tt7286456", title: "Joker", genre: ["Crime", "Drama", "Thriller"], description: "A mentally troubled comedian embarks on a downward spiral of revolution and bloody crime...", year: 2019, director: "Todd Phillips", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Joker" },
            { id: "tt0114369", title: "Se7en", genre: ["Crime", "Drama", "Mystery"], description: "Two detectives, a rookie and a veteran, hunt a serial killer who uses the seven deadly sins as his motives.", year: 1995, director: "David Fincher", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Se7en" },
            { id: "tt1853728", title: "Django Unchained", genre: ["Drama", "Western"], description: "With the help of a German bounty-hunter, a freed slave sets out to rescue his wife from a brutal plantation owner...", year: 2012, director: "Quentin Tarantino", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Django" },
            { id: "tt9362722", title: "Spider-Man: Across the Spider-Verse", genre: ["Animation", "Action", "Adventure"], description: "Miles Morales catapults across the Multiverse, where he encounters a team of Spider-People...", year: 2023, director: "Joaquim Dos Santos", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Spider-Verse" },
            { id: "tt2948372", title: "Zootopia", genre: ["Animation", "Adventure", "Comedy"], description: "In a city of anthropomorphic animals, a rookie bunny cop and a cynical con artist fox must work together...", year: 2016, director: "Byron Howard", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Zootopia" },
            { id: "tt0435761", title: "Toy Story 3", genre: ["Animation", "Adventure", "Comedy"], description: "The toys are mistakenly delivered to a day-care center instead of the attic right before Andy leaves for college...", year: 2010, director: "Lee Unkrich", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Toy+Story+3" },
            { id: "tt1160419", title: "Dune", genre: ["Sci-Fi", "Adventure", "Action"], description: "A noble family becomes embroiled in a war for control over the galaxy's most valuable asset...", year: 2021, director: "Denis Villeneuve", imageUrl: "https://placehold.co/300x450/1a202c/ffffff?text=Dune" }
        ];

        // Retrieve favorite movie IDs from local storage, or initialize an empty array.
        let favoriteMovieIds = JSON.parse(localStorage.getItem('favoriteMovieIds')) || [];

        // --- 2. UI ELEMENT REFERENCES ---
        const getRecommendationsBtn = document.getElementById('get-recommendations-btn');
        const genreCheckboxesContainer = document.getElementById('genre-checkboxes');
        const moviesContainer = document.getElementById('movies-container');
        const favoritesContainer = document.getElementById('favorites-container');

        // --- 3. CORE FUNCTIONS ---

        // Dynamically create genre checkboxes from the movies data
        function populateGenreCheckboxes() {
            const allGenres = new Set(movies.flatMap(movie => movie.genre));
            allGenres.forEach(genre => {
                const label = document.createElement('label');
                label.className = 'flex items-center space-x-2 cursor-pointer';
                label.innerHTML = `
                    <input type="checkbox" value="${genre}" class="h-5 w-5 rounded border-gray-300 text-teal-600 focus:ring-teal-500">
                    <span class="text-slate-700">${genre}</span>
                `;
                genreCheckboxesContainer.appendChild(label);
            });
        }
        
        // Gets all selected genres from the checkboxes
        function getSelectedGenres() {
            const selected = [];
            genreCheckboxesContainer.querySelectorAll('input:checked').forEach(checkbox => {
                selected.push(checkbox.value);
            });
            return selected;
        }
        
        // Filters the main movie list based on the selected genres
        function filterMoviesByGenres(selectedGenres) {
            if (selectedGenres.length === 0) {
                return movies; // If no genres are selected, return all movies.
            }
            // Return movies where at least one of the movie's genres is in the selectedGenres array.
            return movies.filter(movie => movie.genre.some(g => selectedGenres.includes(g)));
        }

        // Creates the HTML for a single movie card
        function createMovieCard(movie) {
            const movieCard = document.createElement('div');
            movieCard.className = 'bg-white rounded-xl shadow-md overflow-hidden movie-card';
            
            const isFavorite = favoriteMovieIds.includes(movie.id);
            const favoriteBtnClass = isFavorite ? 'favorited' : '';
            const favoriteBtnText = isFavorite ? 'Unfavorite' : 'Favorite';

            movieCard.innerHTML = `
                <img src="${movie.imageUrl}" alt="${movie.title} Poster" class="w-full h-auto object-cover">
                <div class="p-4">
                    <h3 class="text-xl font-bold mb-1">${movie.title} (${movie.year})</h3>
                    <p class="text-sm text-slate-500 mb-2"><strong>Genre:</strong> ${movie.genre.join(', ')}</p>
                    <p class="text-slate-600 text-sm mb-4">${movie.description}</p>
                    <button class="favorite-btn w-full py-2 px-4 rounded-lg font-semibold transition-colors duration-200 bg-slate-200 hover:bg-slate-300 ${favoriteBtnClass}" data-movie-id="${movie.id}">
                        ${favoriteBtnText}
                    </button>
                </div>
            `;
            // Add event listener directly to the button within the newly created card
            movieCard.querySelector('.favorite-btn').addEventListener('click', (event) => {
                toggleFavorite(event.target.dataset.movieId);
            });

            return movieCard;
        }

        // Renders a list of movies into a specified container
        function displayMovies(movieList, container) {
            container.innerHTML = ''; // Clear any previous content
            if (movieList.length === 0) {
                container.innerHTML = '<p class="text-slate-500 col-span-full text-center">No movies found for your selection.</p>';
                return;
            }
            movieList.forEach(movie => {
                container.appendChild(createMovieCard(movie));
            });
        }

        // Toggles a movie's favorite status
        function toggleFavorite(movieId) {
            const index = favoriteMovieIds.indexOf(movieId);
            if (index > -1) {
                // Movie is a favorite, so remove it
                favoriteMovieIds.splice(index, 1);
            } else {
                // Movie is not a favorite, so add it
                favoriteMovieIds.push(movieId);
            }
            // Save the updated list to local storage
            localStorage.setItem('favoriteMovieIds', JSON.stringify(favoriteMovieIds));
            // Re-render both lists to reflect the change in favorite status
            handleRecommendationRequest();
            displayFavoriteMovies();
        }

        // Renders the list of favorite movies
        function displayFavoriteMovies() {
            const favMovies = movies.filter(movie => favoriteMovieIds.includes(movie.id));
            displayMovies(favMovies, favoritesContainer);
            if (favMovies.length === 0) {
                favoritesContainer.innerHTML = '<p class="text-slate-500 col-span-full text-center">You haven\'t saved any favorites yet.</p>';
            }
        }

        // Central function to handle recommendation request and display
        function handleRecommendationRequest() {
            const selectedGenres = getSelectedGenres();
            const recommendedMovies = filterMoviesByGenres(selectedGenres);
            displayMovies(recommendedMovies, moviesContainer);
        }

        // --- 4. EVENT LISTENERS & INITIALIZATION ---

        // Main button to get recommendations
        getRecommendationsBtn.addEventListener('click', handleRecommendationRequest);
        
        // Initial setup on page load
        populateGenreCheckboxes();
        displayMovies(movies, moviesContainer); // Display all movies initially
        displayFavoriteMovies();

    });
    </script>
</body>
</html>
