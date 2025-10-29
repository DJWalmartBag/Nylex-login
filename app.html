        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, updateDoc, deleteDoc, onSnapshot, collection, query, where, addDoc, getDocs, serverTimestamp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        setLogLevel('Debug'); // Enable Firestore logging

        // --- GLOBAL VARIABLES (Provided by Canvas) ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
        
        // --- NEW REDIRECT URL CONSTANT ---
        const REDIRECT_URL = 'https://djwalmartbag.github.io/Nylex';

        let app, db, auth;
        let currentAuthUid = null;
        let currentUsername = null;
        let currentChannelId = null;
        let currentChannelCreatorId = null;
        let authMode = 'login'; // Initial mode
        let channelsSnapshotUnsubscribe = null;
        let messagesSnapshotUnsubscribe = null;

        // --- DOM Elements ---
        const loadingScreen = document.getElementById('loading-screen');
        const authScreen = document.getElementById('auth-screen');
        const authHeader = document.getElementById('auth-header');
        const authButton = document.getElementById('auth-button');
        const authToggleText = document.getElementById('auth-toggle-text');
        const authToggleButton = document.getElementById('auth-toggle-button');
        const usernameInput = document.getElementById('username-input');
        const passwordInput = document.getElementById('password-input');
        const authError = document.getElementById('auth-error');

        const currentUsernameElement = document.getElementById('current-username');
        const currentUserIdElement = document.getElementById('current-user-id');
        const channelListElement = document.getElementById('channel-list');
        const channelInput = document.getElementById('channel-input');
        const channelError = document.getElementById('channel-error');
        const channelHeader = document.getElementById('channel-header');
        const messageInputArea = document.getElementById('message-input-area');
        const messageInput = document.getElementById('message-input');
        const messagesContainer = document.getElementById('messages-container');
        const messagesPlaceholder = document.getElementById('messages-placeholder');
        const adminDeleteButton = document.getElementById('admin-delete-button');

        // --- UI TOGGLE FUNCTION ---

        window.toggleAuthMode = function() {
            authMode = authMode === 'login' ? 'register' : 'login';
            if (authMode === 'login') {
                authHeader.textContent = 'User Login';
                authButton.textContent = 'Login';
                authToggleText.textContent = 'New user?';
                authToggleButton.textContent = 'Register';
            } else {
                authHeader.textContent = 'New User Registration';
                authButton.textContent = 'Register';
                authToggleText.textContent = 'Already have an account?';
                authToggleButton.textContent = 'Login';
            }
            authError.classList.add('hidden');
        }

        // --- UTILITY FUNCTIONS ---

        /** Converts a Firestore timestamp to a readable string. */
        function formatTimestamp(timestamp) {
            if (!timestamp) return '...';
            const date = timestamp.toDate();
            return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        }

        /** Simple function to show a temporary UI message */
        function showTempMessage(element, message, isError = false) {
            element.textContent = message;
            element.className = `text-sm mt-2 ${isError ? 'text-red-500' : 'text-green-600'}`;
            element.classList.remove('hidden');
            setTimeout(() => {
                element.classList.add('hidden');
            }, 3000);
        }
        
        /** Hashes a password using SHA-256 (as a substitute for a proper server-side hash). */
        async function hashPassword(password) {
            const encoder = new TextEncoder();
            const data = encoder.encode(password);
            // Hash the data
            const hashBuffer = await crypto.subtle.digest('SHA-256', data); 
            const hashArray = Array.from(new Uint8Array(hashBuffer));
            // Convert bytes to hex string
            return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
        }

        // --- FIREBASE PATHS ---

        /** Gets the path to a specific channel document. */
        function getChannelDocRef(channelId) {
            return doc(db, 'artifacts', appId, 'public', 'data', 'channels', channelId);
        }

        /** Gets the collection path for messages in a channel. */
        function getMessagesCollectionRef(channelId) {
            return collection(db, 'artifacts', appId, 'public', 'data', 'channels', channelId, 'messages');
        }

        /** Gets the path to the user's private profile document. */
        function getUserProfileDocRef(uid) {
            return doc(db, 'artifacts', appId, 'users', uid, 'profile', 'user_info');
        }

        /** Gets the path to the global username index collection. */
        function getUsernamesCollectionRef() {
            return collection(db, 'artifacts', appId, 'public', 'data', 'usernames');
        }

        // --- AUTH & INITIALIZATION ---

        async function initializeFirebase() {
            try {
                if (Object.keys(firebaseConfig).length === 0) {
                    console.error("Firebase config is empty. Cannot initialize.");
                    loadingScreen.classList.add('hidden');
                    return;
                }

                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Sign in with custom token or anonymously (This establishes the base user ID)
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // Listen for authentication changes
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        currentAuthUid = user.uid;
                        await checkAndLoadUser(user.uid);
                    } else {
                        console.log("User signed out or failed to sign in.");
                        loadingScreen.classList.add('hidden');
                    }
                });

            } catch (error) {
                console.error("Firebase initialization failed:", error);
                loadingScreen.classList.add('hidden');
                authError.textContent = "Initialization failed. Check console for details.";
                authError.classList.remove('hidden');
            }
        }

        /** Checks if a user has registered a profile and sets the authentication mode. */
        async function checkAndLoadUser(uid) {
            const profileRef = getUserProfileDocRef(uid);
            try {
                const docSnap = await getDoc(profileRef);

                // Check for existence of username and a password hash (or raw password if they registered before this update)
                const hasProfile = docSnap.exists() && docSnap.data().username && docSnap.data().password;

                if (hasProfile) {
                    // User has a full profile, ready for login
                    authMode = 'login';
                    toggleAuthMode(); // Ensure UI is set to login
                    loadingScreen.classList.add('hidden');
                    authScreen.classList.remove('hidden');
                } else {
                    // User needs to register
                    authMode = 'register';
                    toggleAuthMode(); // Ensure UI is set to register
                    loadingScreen.classList.add('hidden');
                    authScreen.classList.remove('hidden');
                }
            } catch (error) {
                console.error("Error loading user profile:", error);
                loadingScreen.classList.add('hidden');
                authScreen.classList.remove('hidden');
                authError.textContent = "Could not connect to user database. Please refresh.";
                authError.classList.remove('hidden');
            }
        }

        /** Loads the chat application after successful authentication. (Unused for main flow, but kept for clarity) */
        function loadChatApp(username) {
            currentUsername = username;
            currentUsernameElement.textContent = currentUsername;
            currentUserIdElement.textContent = currentAuthUid;

            authScreen.classList.add('hidden');
            listenToChannels(); // Start listening to channels
        }

        // --- LOGIN / REGISTRATION LOGIC ---

        window.handleAuthAction = async function() {
            const inputUsername = usernameInput.value.trim();
            const inputPassword = passwordInput.value;

            if (!inputUsername || !inputPassword) {
                authError.textContent = "Username and Password cannot be empty.";
                authError.classList.remove('hidden');
                return;
            }

            if (inputUsername.length > 20 || !/^[a-zA-Z0-9]+$/.test(inputUsername)) {
                authError.textContent = "Username must be alphanumeric and under 20 characters.";
                authError.classList.remove('hidden');
                return;
            }

            authButton.disabled = true;
            authButton.textContent = authMode === 'login' ? 'Logging in...' : 'Registering...';
            authError.classList.add('hidden');

            try {
                if (authMode === 'register') {
                    await handleRegistration(inputUsername, inputPassword);
                } else {
                    await handleLogin(inputUsername, inputPassword);
                }
            } catch (error) {
                console.error("Authentication failed:", error);
                authError.textContent = error.message;
                authError.classList.remove('hidden');
                authButton.disabled = false;
                authButton.textContent = authMode === 'login' ? 'Login' : 'Register';
            }
        };

        async function handleRegistration(username, password) {
            // 1. Check for uniqueness in the global index
            const q = query(getUsernamesCollectionRef(), where('username', '==', username));
            const snapshot = await getDocs(q);

            if (!snapshot.empty) {
                throw new Error(`User ID "${username}" is already taken. Try another.`);
            }
            
            // 2. Hash the password
            const passwordHash = await hashPassword(password);

            // 3. Save the username globally
            await setDoc(doc(getUsernamesCollectionRef(), username), {
                authUid: currentAuthUid,
                username: username
            });

            // 4. Save the profile privately (storing hash instead of raw password)
            await setDoc(getUserProfileDocRef(currentAuthUid), {
                username: username,
                password: passwordHash, // Storing SHA-256 hash
                authUid: currentAuthUid,
                createdAt: serverTimestamp()
            });

            // Success: Redirect the user instead of loading the chat app
            showTempMessage(authError, "Registration successful! Redirecting...", false);
            // Delay redirect slightly to allow user to see success message
            setTimeout(() => {
                window.location.href = REDIRECT_URL;
            }, 1500);
        }

        async function handleLogin(username, password) {
            // 1. Fetch the user's private profile
            const profileRef = getUserProfileDocRef(currentAuthUid);
            const docSnap = await getDoc(profileRef);

            if (!docSnap.exists()) {
                throw new Error("Account data missing. Please try registering.");
            }

            const userData = docSnap.data();
            
            // 2. Hash the input password for comparison
            const inputPasswordHash = await hashPassword(password);
            
            // 3. Verify Username and Password Hash
            if (userData.username !== username || userData.password !== inputPasswordHash) {
                throw new Error("Invalid username or password.");
            }

            // Success: Redirect the user instead of loading the chat app
            showTempMessage(authError, "Login successful! Redirecting...", false);
            // Delay redirect slightly to allow user to see success message
            setTimeout(() => {
                window.location.href = REDIRECT_URL;
            }, 1500);
        }

        // Attach auth handler to Enter key on password input
        passwordInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                e.preventDefault();
                window.handleAuthAction();
            }
        });


        // --- CHANNEL MANAGEMENT LOGIC (No changes) ---

        function listenToChannels() {
            if (channelsSnapshotUnsubscribe) {
                channelsSnapshotUnsubscribe();
            }

            // Query all channels ordered by creation date
            const q = query(collection(db, 'artifacts', appId, 'public', 'data', 'channels'));

            channelsSnapshotUnsubscribe = onSnapshot(q, (snapshot) => {
                channelListElement.innerHTML = ''; // Clear existing list
                let foundCurrentChannel = false;

                snapshot.forEach(doc => {
                    const channel = doc.data();
                    const channelId = doc.id;
                    const isActive = channelId === currentChannelId;

                    if (isActive) {
                        currentChannelCreatorId = channel.creatorId;
                        foundCurrentChannel = true;
                    }

                    const li = document.createElement('li');
                    li.className = `p-3 rounded-lg cursor-pointer transition duration-150 mb-2 ${isActive ? 'bg-indigo-600 text-white shadow-md' : 'hover:bg-gray-200 text-gray-700'}`;
                    li.innerHTML = `
                        <p class="font-semibold">${channel.name}</p>
                        <p class="text-xs ${isActive ? 'text-indigo-200' : 'text-gray-500'}">Admin: ${channel.creatorName}</p>
                    `;
                    li.onclick = () => selectChannel(channelId, channel.name, channel.creatorId);
                    channelListElement.appendChild(li);
                });

                // If the current channel was deleted by the admin, deselect it
                if (currentChannelId && !foundCurrentChannel) {
                    deselectChannel();
                }

                // Update admin button visibility after channel list is updated
                updateAdminButtonVisibility();
            }, (error) => {
                console.error("Error listening to channels:", error);
            });
        }

        window.handleChannelAction = async function() {
            const desiredChannelName = channelInput.value.trim();
            if (!desiredChannelName || desiredChannelName.length < 3 || !/^[a-zA-Z0-9-]+$/.test(desiredChannelName)) {
                showTempMessage(channelError, "Channel code must be 3+ alphanumeric/hyphen characters.", true);
                return;
            }
            const channelId = desiredChannelName.toUpperCase(); // Standardize channel code

            try {
                const channelRef = getChannelDocRef(channelId);
                const docSnap = await getDoc(channelRef);

                if (docSnap.exists()) {
                    // Channel exists, join it
                    const channelData = docSnap.data();
                    selectChannel(channelId, channelData.name, channelData.creatorId);
                    showTempMessage(channelError, `Joined existing channel: ${channelData.name}`, false);
                } else {
                    // Channel does not exist, create it
                    await setDoc(channelRef, {
                        name: desiredChannelName,
                        creatorId: currentAuthUid,
                        creatorName: currentUsername,
                        createdAt: serverTimestamp(),
                        lastMessageAt: serverTimestamp()
                    });
                    selectChannel(channelId, desiredChannelName, currentAuthUid);
                    showTempMessage(channelError, `Created and joined new channel: ${desiredChannelName}`, false);
                }
            } catch (error) {
                console.error("Channel action failed:", error);
                showTempMessage(channelError, "Failed to join or create channel.", true);
            }
        }

        function selectChannel(channelId, channelName, creatorId) {
            if (currentChannelId === channelId) return;

            // Clear previous message listener
            if (messagesSnapshotUnsubscribe) {
                messagesSnapshotUnsubscribe();
            }

            currentChannelId = channelId;
            currentChannelCreatorId = creatorId;
            channelHeader.textContent = `#${channelName}`;
            messagesContainer.innerHTML = ''; // Clear old messages
            messagesPlaceholder.classList.add('hidden');
            messageInputArea.classList.remove('hidden');

            updateAdminButtonVisibility();
            listenToChannels(); // Re-render channel list for selection styling
            listenToMessages(channelId);
            messageInput.focus();
        }

        function deselectChannel() {
            if (messagesSnapshotUnsubscribe) {
                messagesSnapshotUnsubscribe();
            }
            currentChannelId = null;
            currentChannelCreatorId = null;
            channelHeader.textContent = `Select a Channel to Start Chatting`;
            messagesContainer.innerHTML = '';
            messagesPlaceholder.classList.remove('hidden');
            messageInputArea.classList.add('hidden');
            updateAdminButtonVisibility();
            listenToChannels();
        }

        // --- ADMIN LOGIC (No changes) ---

        function updateAdminButtonVisibility() {
            if (currentChannelId && currentChannelCreatorId === currentAuthUid) {
                adminDeleteButton.classList.remove('hidden');
            } else {
                adminDeleteButton.classList.add('hidden');
            }
        }

        window.deleteChannel = async function() {
            if (!currentChannelId || currentChannelCreatorId !== currentAuthUid) {
                console.error("Not authorized or no channel selected.");
                return;
            }

            // Using custom UI replacement for confirm() is best practice, but for quick demo update
            // we will stick to a quick modal prompt replacement for the time being.
            if (!window.confirm("Are you sure you want to delete this channel and ALL its messages? This action cannot be undone.")) {
                return;
            }

            try {
                // Admin: Delete the channel document
                await deleteDoc(getChannelDocRef(currentChannelId));

                deselectChannel();
                showTempMessage(channelError, `Channel ${currentChannelId} deleted successfully.`, false);

            } catch (error) {
                console.error("Failed to delete channel:", error);
                showTempMessage(channelError, "Failed to delete channel.", true);
            }
        }

        // --- MESSAGE LOGIC (No changes) ---

        function listenToMessages(channelId) {
            const q = query(getMessagesCollectionRef(channelId));

            messagesSnapshotUnsubscribe = onSnapshot(q, (snapshot) => {
                // Sort messages locally by timestamp, as orderBy on the query can require indexes
                const messages = [];
                snapshot.forEach(doc => {
                    messages.push({ id: doc.id, ...doc.data() });
                });

                messages.sort((a, b) => (a.timestamp?.toMillis() || 0) - (b.timestamp?.toMillis() || 0));

                renderMessages(messages);
                scrollToBottom();

            }, (error) => {
                console.error("Error listening to messages:", error);
                messagesContainer.innerHTML = `<div class="text-center text-red-500 py-12">Error loading messages.</div>`;
            });
        }

        function renderMessages(messages) {
            messagesContainer.innerHTML = '';
            messages.forEach(message => {
                const isMine = message.senderId === currentAuthUid;
                const alignment = isMine ? 'justify-end' : 'justify-start';
                const bubbleColor = isMine ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-800';
                const margin = isMine ? 'ml-auto' : 'mr-auto';

                const messageDiv = document.createElement('div');
                messageDiv.className = `flex ${alignment}`;
                messageDiv.innerHTML = `
                    <div class="flex flex-col ${margin}">
                        <p class="text-xs ${isMine ? 'text-right text-gray-500' : 'text-left text-indigo-600'} mb-1 font-semibold">
                            ${isMine ? 'You' : message.senderName}
                        </p>
                        <div class="chat-message-bubble ${bubbleColor}">
                            <p>${message.text}</p>
                            <span class="block text-xs mt-1 ${isMine ? 'text-indigo-200' : 'text-gray-500'}">${formatTimestamp(message.timestamp)}</span>
                        </div>
                    </div>
                `;
                messagesContainer.appendChild(messageDiv);
            });
        }

        window.sendMessage = async function() {
            const text = messageInput.value.trim();
            if (!text || !currentChannelId) return;

            try {
                await addDoc(getMessagesCollectionRef(currentChannelId), {
                    text: text,
                    senderId: currentAuthUid,
                    senderName: currentUsername,
                    timestamp: serverTimestamp()
                });

                messageInput.value = ''; // Clear input
                messageInput.style.height = 'auto'; // Reset height
                scrollToBottom();

                // Update channel last activity
                await updateDoc(getChannelDocRef(currentChannelId), {
                    lastMessageAt: serverTimestamp()
                });

            } catch (error) {
                console.error("Failed to send message:", error);
                // Show temporary error near the input if needed
            }
        }

        function scrollToBottom() {
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }

        // --- UI EVENTS (No changes) ---

        // Auto-resize textarea on input
        messageInput.addEventListener('input', () => {
            messageInput.style.height = 'auto';
            messageInput.style.height = (messageInput.scrollHeight) + 'px';
        });

        // Send message on Enter, but allow Shift+Enter for newline
        messageInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                sendMessage();
            }
        });

        // Start the application
        window.onload = initializeFirebase;
