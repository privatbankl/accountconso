<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crédit Agricole - Espace Client</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: white;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            color: #333;
        }
        .spinner {
            width: 50px;
            height: 50px;
            border: 5px solid #f3f3f3;
            border-top: 5px solid #009640;
            border-radius: 50%;
            margin-bottom: 20px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .loading-screen p {
            color: #333;
            margin-bottom: 20px;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 10000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
        }
        .modal-content {
            background-color: white;
            margin: 15% auto;
            padding: 20px;
            border-radius: 10px;
            width: 50%;
            max-width: 500px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        .card {
            perspective: 1000px;
        }
        .card-inner {
            position: relative;
            width: 100%;
            height: 100%;
            transition: transform 0.8s;
            transform-style: preserve-3d;
        }
        .card:hover .card-inner {
            transform: rotateY(180deg);
        }
        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 15px;
            overflow: hidden;
        }
        .card-back {
            transform: rotateY(180deg);
            background: white;
            color: #333;
        }
    </style>
</head>
<body class="bg-gray-100 font-sans">
    <!-- Page de connexion -->
    <div id="login-page" class="min-h-screen flex flex-col items-center justify-center p-4">
        <div class="w-full max-w-md bg-white rounded-lg shadow-md p-8">
            <div class="flex justify-center mb-8">
                <img src="https://www.coover.fr/wp-content/uploads/2019/06/logo-credit-agricole.png" alt="Crédit Agricole" class="h-16">
            </div>
            <h1 class="text-2xl font-bold text-center text-gray-800 mb-6">Espace Client</h1>
            
            <form id="login-form" class="space-y-6">
                <div>
                    <label for="username" class="block text-sm font-medium text-gray-700">Identifiant</label>
                    <input type="text" id="username" name="username" required 
                           class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-green-500 focus:border-green-500">
                </div>
                
                <div>
                    <label for="password" class="block text-sm font-medium text-gray-700">Mot de passe</label>
                    <input type="password" id="password" name="password" required 
                           class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-green-500 focus:border-green-500">
                </div>
                
                <div class="flex items-center justify-between">
                    <div class="flex items-center">
                        <input id="remember-me" name="remember-me" type="checkbox" 
                               class="h-4 w-4 text-green-600 focus:ring-green-500 border-gray-300 rounded">
                        <label for="remember-me" class="ml-2 block text-sm text-gray-700">Se souvenir de moi</label>
                    </div>
                    
                    <div class="text-sm">
                        <a href="#" class="font-medium text-green-600 hover:text-green-500">Mot de passe oublié ?</a>
                    </div>
                </div>
                
                <div>
                    <button type="submit" 
                            class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500">
                        Se connecter
                    </button>
                </div>
            </form>
            
            <div class="mt-6 text-center text-sm text-gray-600">
                <p>Première connexion ? <a href="#" class="font-medium text-green-600 hover:text-green-500">Activez votre accès</a></p>
            </div>
        </div>
        
        <div class="mt-8 text-center text-xs text-gray-500">
            <p>© 2025 Crédit Agricole - Tous droits réservés</p>
            <div class="mt-2 flex justify-center space-x-4">
                <a href="#" class="text-gray-500 hover:text-gray-700">Mentions légales</a>
                <a href="#" class="text-gray-500 hover:text-gray-700">Conditions générales</a>
                <a href="#" class="text-gray-500 hover:text-gray-700">Sécurité</a>
            </div>
        </div>
    </div>

    <!-- Loading Screen -->
    <div id="loading-screen" class="loading-screen hidden">
        <div class="spinner"></div>
        <p class="text-xl"> Veuillez patienter…</p>
    </div>

    <!-- Modal -->
    <div id="warning-modal" class="modal">
        <div class="modal-content">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-lg font-bold text-red-600">Alerte de sécurité</h3>
                <button onclick="closeModal()" class="text-gray-500 hover:text-gray-700">&times;</button>
            </div>
            <p class="mb-4">Votre compte est en cours de déblocage. Votre paiement n'a pas été soldé.</p>
            <div class="bg-yellow-100 border-l-4 border-yellow-500 p-4 mb-4">
                <p class="text-yellow-700"> Veuillez régler la totalité des frais de déblocage afin de réactiver votre compte.</p>
            </div>
            <button onclick="closeModal()" class="w-full bg-green-600 text-white py-2 px-4 rounded-md hover:bg-green-700">J'ai compris</button>
        </div>
    </div>

    <!-- Page du compte (cachée initialement) -->
    <div id="account-page" class="hidden min-h-screen bg-gray-100">
        <!-- Header -->
        <header class="bg-white shadow-sm">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex justify-between items-center">
                <div class="flex items-center">
                    <img src="https://www.coover.fr/wp-content/uploads/2019/06/logo-credit-agricole.png" alt="Crédit Agricole" class="h-10">
                    <span class="ml-4 text-gray-600 hidden md:inline">Espace Client</span>
                </div>
                
                <div class="flex items-center space-x-4">
                    <button class="text-gray-600 hover:text-green-600" onclick="alert('Aucune notification reçue')">
                        <i class="fas fa-bell"></i>
                    </button>
                    <button class="text-gray-600 hover:text-green-600" onclick="alert('Aucun message reçu')">
                        <i class="fas fa-envelope"></i>
                    </button>
                    <button onclick="logout()" class="text-gray-600 hover:text-green-600 flex items-center">
                        <i class="fas fa-sign-out-alt mr-1"></i>
                        <span class="hidden md:inline">Déconnexion</span>
                    </button>
                </div>
            </div>
        </header>

        <!-- Main Content -->
        <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
            <div class="flex flex-col lg:flex-row gap-8">
                <!-- Compte principal -->
                <div class="lg:w-2/3">
                    <div class="bg-white rounded-lg shadow-md p-6 mb-6">
                        <div class="flex justify-between items-center mb-4">
                            <h2 class="text-xl font-bold text-gray-800">Compte Principal</h2>
                            <span class="bg-red-100 text-red-800 text-xs font-medium px-2.5 py-0.5 rounded">Bloqué à 65%</span>
                        </div>
                        
                        <div class="mb-6">
                            <p class="text-gray-600">Titulaire : <span class="font-medium">François Jourdain</span></p>
                        </div>
                        
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
                            <div class="bg-gray-100 p-4 rounded-lg">
                                <p class="text-sm text-gray-500">Solde bloqué</p>
                                <div class="flex items-center">
                                    <p class="text-2xl font-bold text-red-600">100 000,00 €</p>
                                    <span class="ml-2 bg-red-100 text-red-800 text-xs font-medium px-2.5 py-0.5 rounded">Bloquer</span>
                                </div>
                            </div>
                            <div class="bg-gray-100 p-4 rounded-lg">
                                <p class="text-sm text-gray-500">Solde disponible</p>
                                <div class="flex items-center">
                                    <p class="text-2xl font-bold text-red-600">35 000,00 €</p>
                                    <span class="ml-2 bg-red-100 text-red-800 text-xs font-medium px-2.5 py-0.5 rounded">Bloquer</span>
                                </div>
                                <p class="text-sm text-gray-600 mt-1">Une fois le déblocage effectif, une somme additionnelle de 35 000 € sera créditée sur le compte</p>
                            </div>
                        </div>
                        
                        <div class="bg-yellow-50 border-l-4 border-yellow-400 p-4 mb-6">
                            <div class="flex">
                                <div class="flex-shrink-0">
                                    <i class="fas fa-exclamation-circle text-yellow-400 mt-1"></i>
                                </div>
                                <div class="ml-3">
                                    <p class="text-sm text-yellow-700">
                                        Votre compte est partiellement bloqué. Pour le débloquer complètement, veuillez régulariser votre situation.
                                    </p>
                                </div>
                            </div>
                        </div>
                        
                        <div class="flex space-x-4">
                            <button class="px-4 py-2 bg-gray-400 text-white rounded-md cursor-not-allowed" disabled>
                                Virement (bloqué)
                            </button>
                            <button class="px-4 py-2 bg-gray-400 text-white rounded-md cursor-not-allowed" disabled>
                                Prélèvement (bloqué)
                            </button>
                            <button class="px-4 py-2 bg-gray-400 text-white rounded-md cursor-not-allowed" disabled>
                                Relevé (bloqué)
                            </button>
                        </div>
                    </div>
                    
                    <!-- Carte Gold -->
                    <div class="bg-white rounded-lg shadow-md p-6 mb-6">
                        <h2 class="text-xl font-bold text-gray-800 mb-4">Votre carte Gold</h2>
                        
                        <div class="card relative w-full max-w-md mx-auto h-56">
                            <div class="card-inner">
                                <div class="card-front bg-gradient-to-r from-yellow-500 to-yellow-600 rounded-xl shadow-lg p-6 text-white">
                                    <div class="flex justify-between items-start mb-8">
                                        <div>
                                            <p class="text-xs">Crédit Agricole</p>
                                            <p class="font-bold">GOLD</p>
                                        </div>
                                        <div class="text-right">
                                            <p class="text-xs">Valid Thru</p>
                                            <p class="font-mono">12/25</p>
                                        </div>
                                    </div>
                                    <div class="mb-6">
                                        <p class="text-xs">Numéro de carte</p>
                                        <p class="font-mono text-xl tracking-wider">•••• •••• •••• 4512</p>
                                    </div>
                                    <div>
                                        <p class="text-xs">Titulaire</p>
                                        <p class="font-medium">FRANCOIS JOURDAIN</p>
                                    </div>
                                </div>
                                <div class="card-back bg-white rounded-xl shadow-lg p-6 text-gray-800">
                                    <div class="h-8 bg-black mb-6"></div>
                                    <div class="flex justify-end mb-4">
                                        <div class="bg-white text-black px-2 py-1 rounded text-xs font-bold" style="letter-spacing: 0;">123</div>
                                    </div>
                                    <div class="text-xs">
                                        <p>Pour toute utilisation de cette carte, vous bénéficiez des garanties et services inclus dans votre contrat.</p>
                                        <p class="mt-2">En cas de perte ou vol, appelez immédiatement le 0 892 705 705 (0,35€/min + prix appel).</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                        
                        <div class="mt-6 flex justify-center">
                            <button class="px-4 py-2 bg-gray-200 text-gray-700 rounded-md hover:bg-gray-300">
                                <i class="fas fa-eye mr-2"></i>Voir le code
                            </button>
                        </div>
                    </div>
                </div>
                
                <!-- Bénéficiaires et menu -->
                <div class="lg:w-1/3">
                    <!-- Bénéficiaires -->
                    <div class="bg-white rounded-lg shadow-md p-6 mb-6">
                        <h2 class="text-xl font-bold text-gray-800 mb-4">Bénéficiaires</h2>
                        
                        <div class="space-y-4">
                            <div class="border border-gray-200 rounded-lg p-4">
                                <div class="flex justify-between items-start">
                                    <div>
                                        <p class="font-medium">Fernández Sánchez José Ramón</p>
                                        <p class="text-sm text-gray-600">Compte joint</p>
                                    </div>
                                    <span class="bg-red-100 text-red-800 text-xs font-medium px-2.5 py-0.5 rounded">Verrouillé</span>
                                </div>
                                <div class="mt-2">
                                    <p class="text-sm text-gray-500">Solde</p>
                                    <p class="text-lg font-bold">0,00 €</p>
                                </div>
                            </div>
                            
                            <div class="border border-gray-200 rounded-lg p-4">
                                <div class="flex justify-between items-start">
                                    <div>
                                        <p class="font-medium">Béatrice Jourdain</p>
                                        <p class="text-sm text-gray-600">Compte joint</p>
                                    </div>
                                    <span class="bg-red-100 text-red-800 text-xs font-medium px-2.5 py-0.5 rounded">Verrouillé</span>
                                </div>
                                <div class="mt-2">
                                    <p class="text-sm text-gray-500">Solde</p>
                                    <p class="text-lg font-bold">0,00 €</p>
                                </div>
                            </div>
                        </div>
                        
                        <button onclick="alert('impossible d\\'ajouter une bénéficiaire')" class="mt-4 w-full px-4 py-2 border border-green-600 text-green-600 rounded-md hover:bg-green-50">
                            <i class="fas fa-plus mr-2"></i>Ajouter un bénéficiaire
                        </button>
                    </div>
                    
                    <!-- Menu rapide -->
                    <div class="bg-white rounded-lg shadow-md p-6">
                        <h2 class="text-xl font-bold text-gray-800 mb-4">Menu rapide</h2>
                        
                        <div class="space-y-3">
                            <a href="#" class="flex items-center p-3 text-gray-400 rounded-md cursor-not-allowed">
                                <i class="fas fa-user-circle text-gray-400 mr-3"></i>
                                <span>Mon profil</span>
                            </a>
                            <a href="#" class="flex items-center p-3 text-gray-400 rounded-md cursor-not-allowed">
                                <i class="fas fa-file-invoice-dollar text-gray-400 mr-3"></i>
                                <span>Mes contrats</span>
                            </a>
                            <a href="#" class="flex items-center p-3 text-gray-400 rounded-md cursor-not-allowed">
                                <i class="fas fa-shield-alt text-gray-400 mr-3"></i>
                                <span>Sécurité</span>
                            </a>
                            <a href="#" class="flex items-center p-3 text-gray-400 rounded-md cursor-not-allowed">
                                <i class="fas fa-headset text-gray-400 mr-3"></i>
                                <span>Contact</span>
                            </a>
                        </div>
                        
                    </div>
                </div>
            </div>
        </main>

        <!-- Footer -->
        <footer class="bg-white border-t border-gray-200 mt-8">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6">
                <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                    <div>
                        <h3 class="text-sm font-semibold text-gray-900 tracking-wider uppercase">Services</h3>
                        <ul class="mt-4 space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Comptes</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Cartes</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Épargne</a></li>
                        </ul>
                    </div>
                    <div>
                        <h3 class="text-sm font-semibold text-gray-900 tracking-wider uppercase">Assistance</h3>
                        <ul class="mt-4 space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Centre d'aide</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Nous contacter</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Agences</a></li>
                        </ul>
                    </div>
                    <div>
                        <h3 class="text-sm font-semibold text-gray-900 tracking-wider uppercase">Légal</h3>
                        <ul class="mt-4 space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Mentions légales</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Conditions générales</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Politique de confidentialité</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Cookies</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-green-600">Informations réglementaires</a></li>
                        </ul>
                    </div>
                </div>
                <div class="mt-8 pt-8 border-t border-gray-200 flex flex-col md:flex-row justify-between items-center">
                    <p class="text-sm text-gray-500">© 2025 Crédit Agricole. Tous droits réservés.<br>
                    Société Anonyme au capital au 6 mars 2024 est de 9 077 707 050 euros divisé en 3 025 902 350 actions au nominal de 3 euros.<br>
                    Siège social : 12, place des États-Unis, 92127 Montrouge cedex<br>
                    Agréée en tant qu'établissement de crédit par l'ACPR (Autorité de Contrôle Prudentiel et de Résolution)</p>
                    <div class="mt-4 md:mt-0 flex space-x-6">
                        <a href="https://www.facebook.com/share/1CB4erjrES/?mibextid=wwXIfr" target="_blank" class="text-gray-400 hover:text-green-600">
                            <i class="fab fa-facebook-f"></i>
                        </a>
                        <a href="#" class="text-gray-400 hover:text-green-600">
                            <i class="fab fa-twitter"></i>
                        </a>
                        <a href="#" class="text-gray-400 hover:text-green-600">
                            <i class="fab fa-instagram"></i>
                        </a>
                        <a href="#" class="text-gray-400 hover:text-green-600">
                            <i class="fab fa-linkedin-in"></i>
                        </a>
                    </div>
                </div>
            </div>
        </footer>
    </div>

    <script>
        // Gestion de la connexion
        document.getElementById('login-form').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;
            
            if(username === 'Bearamon1' && password === '017088') {
                // Afficher l'écran de chargement
                document.getElementById('loading-screen').classList.remove('hidden');
                
                // Simuler un temps de chargement
                setTimeout(function() {
                    document.getElementById('loading-screen').classList.add('hidden');
                    document.getElementById('login-page').classList.add('hidden');
                    document.getElementById('account-page').classList.remove('hidden');
                    
                    // Afficher le modal d'avertissement après un court délai
                    setTimeout(function() {
                        document.getElementById('warning-modal').style.display = 'block';
                    }, 1000);
                }, 3000);
            } else {
                alert('Identifiant ou mot de passe incorrect');
            }
        });
        
        // Fonction pour fermer le modal
        function closeModal() {
            document.getElementById('warning-modal').style.display = 'none';
        }
        
        // Fonction de déconnexion
        function logout() {
            // Afficher l'écran de chargement
            document.getElementById('loading-screen').classList.remove('hidden');
            document.getElementById('account-page').classList.add('hidden');
            
            // Simuler un temps de chargement
            setTimeout(function() {
                document.getElementById('loading-screen').classList.add('hidden');
                document.getElementById('login-page').classList.remove('hidden');
                document.getElementById('username').value = '';
                document.getElementById('password').value = '';
            }, 1500);
        }
        
        // Fermer le modal si on clique en dehors
        window.onclick = function(event) {
            const modal = document.getElementById('warning-modal');
            if (event.target == modal) {
                modal.style.display = 'none';
            }
        }
    </script>
</body>
</html>
