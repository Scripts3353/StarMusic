import React, { useState, useEffect, useRef } from 'react';
import { 
  Play, Pause, SkipForward, SkipBack, Shuffle, Repeat, 
  Download, Check, Music, Menu, X, Upload, HardDrive, 
  Settings, Lock, Key, Sparkles, Cloud, Trash2, Library, 
  AlertTriangle, Plus // <-- FIX: Added Plus here
} from 'lucide-react';
import { initializeApp } from 'firebase/app';
import { 
  getAuth, signInAnonymously, onAuthStateChanged, 
  signInWithCustomToken 
} from 'firebase/auth';
import { 
  getFirestore, collection, addDoc, query, 
  onSnapshot, deleteDoc, doc, setDoc, serverTimestamp, 
  getDocs 
} from 'firebase/firestore';

// --- CONFIGURATION ---
const ADMIN_PASSWORD_HASH = 'YW9sem41a2lkcw=='; 
const DEMO_TRACKS = [
  // These are initial seeds. They will be added to Firestore if the public collection is empty.
  {
    id: 't1', title: 'Cyberpunk City', artist: 'Techno Dreams', album: 'Neon Horizon', duration: 184, genre: 'Synthwave', 
    coverUrl: 'https://images.unsplash.com/photo-1594736797933-d0501ba2fe65?w=400&q=80',
    audioUrl: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3'
  },
  {
    id: 't2', title: 'Deep Focus', artist: 'Mindful State', album: 'Flow', duration: 245, genre: 'Ambient', 
    coverUrl: 'https://images.unsplash.com/photo-1519681393784-d120267933ba?w=400&q=80',
    audioUrl: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3'
  },
  {
    id: 't3', title: 'Midnight Drive', artist: 'Lofi Core', album: 'Night Shift', duration: 210, genre: 'Lofi', 
    coverUrl: 'https://images.unsplash.com/photo-1493225255756-d9584f8606e9?w=400&q=80',
    audioUrl: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3'
  }
];

// --- INDEXED DB HELPER (Browser Storage for Offline Music) ---
const DB_NAME = 'SonicStreamDB';
const DB_VERSION = 1;
const STORE_TRACKS = 'tracks';

const initDB = () => {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open(DB_NAME, DB_VERSION);
    request.onerror = () => {
      console.error("IDB Error:", request.error);
      reject(request.error);
    };
    request.onsuccess = () => resolve(request.result);
    request.onupgradeneeded = (event) => {
      const db = event.target.result;
      if (!db.objectStoreNames.contains(STORE_TRACKS)) {
        db.createObjectStore(STORE_TRACKS, { keyPath: 'id' });
      }
    };
  });
};

const saveTrackToIDB = async (track, blob) => {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const tx = db.transaction(STORE_TRACKS, 'readwrite');
    const store = tx.objectStore(STORE_TRACKS);
    store.put({ ...track, audioBlob: blob, savedAt: new Date() });
    tx.oncomplete = () => resolve(true);
    tx.onerror = () => {
      console.error("IDB Write Error:", tx.error);
      reject(tx.error);
    };
  });
};

const getTracksFromIDB = async () => {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const tx = db.transaction(STORE_TRACKS, 'readonly');
    const store = tx.objectStore(STORE_TRACKS);
    const request = store.getAll();
    request.onsuccess = () => resolve(request.result);
    request.onerror = () => reject(request.error);
  });
};

const deleteTrackFromIDB = async (id) => {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const tx = db.transaction(STORE_TRACKS, 'readwrite');
    const store = tx.objectStore(STORE_TRACKS);
    store.delete(id);
    tx.oncomplete = () => resolve(true);
    tx.onerror = () => reject(tx.error);
  });
};

// --- FIREBASE SETUP ---
let db, auth, appId;
try {
  const firebaseConfig = JSON.parse(__firebase_config);
  const app = initializeApp(firebaseConfig);
  auth = getAuth(app);
  db = getFirestore(app);
  appId = typeof __app_id !== 'undefined' ? __app_id : 'sonic-default';
} catch (e) {
  console.error("Firebase initialization failed:", e);
}
// Firestore paths
const getUserPlaylistPath = (uid) => collection(db, 'artifacts', appId, 'users', uid, 'playlists');
const getPublicTracksPath = () => collection(db, 'artifacts', appId, 'public', 'data', 'tracks');


// --- LLM API INTERACTION ---
const analyzeTrackWithGemini = async (track) => {
  const prompt = `Analyze the track titled "${track.title}" by "${track.artist}" in the ${track.genre} genre. Provide a single paragraph descriptive analysis (about 50 words) that includes potential mood, influences, and a recommendation of a similar imaginary artist/album.`;
  const apiKey = "";
  const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;

  const payload = {
    contents: [{ parts: [{ text: prompt }] }],
    systemInstruction: {
        parts: [{ text: "You are a witty, insightful, and knowledgeable music critic focused on electronic and indie genres." }]
    },
  };

  try {
    const response = await fetch(apiUrl, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    });

    if (!response.ok) {
        throw new Error(`API call failed with status: ${response.status}`);
    }

    const result = await response.json();
    const text = result.candidates?.[0]?.content?.parts?.[0]?.text;
    return text || "AI analysis failed to generate content.";
  } catch (error) {
    console.error("Gemini API Error:", error);
    return `Error: Could not connect to the Gemini API. (${error.message})`;
  }
};


// --- MAIN APP COMPONENT ---
export default function SonicStreamApp() {
  const [user, setUser] = useState(null);
  const [view, setView] = useState('stream'); // stream, library, admin
  const [currentTrack, setCurrentTrack] = useState(null);
  const [isPlaying, setIsPlaying] = useState(false);
  const [queue, setQueue] = useState([]); // Cloud Tracks
  const [localLibrary, setLocalLibrary] = useState([]); // Offline Tracks
  const [playlists, setPlaylists] = useState([]);
  const [downloadingId, setDownloadingId] = useState(null);
  const [currentTime, setCurrentTime] = useState(0);
  const [duration, setDuration] = useState(0);
  const [playMode, setPlayMode] = useState('all'); // all, one, shuffle
  const [volume, setVolume] = useState(1);
  const [showSettingsModal, setShowSettingsModal] = useState(false);
  const [showAnalyzerModal, setShowAnalyzerModal] = useState(false);
  const [isAdmin, setIsAdmin] = useState(false);
  const [adminPassword, setAdminPassword] = useState('');
  const [llmAnalysis, setLlmAnalysis] = useState('Loading analysis...');
  const [sidebarOpen, setSidebarOpen] = useState(false);
  const [isAuthReady, setIsAuthReady] = useState(false);

  const audioRef = useRef(new Audio());
  const fileInputRef = useRef(null);

  // Helper to decode password hash (used to check admin credentials)
  const decodeBase64 = (hash) => {
    try {
      return atob(hash);
    } catch (e) {
      console.error("Base64 decoding failed:", e);
      return '';
    }
  };

  // Auth & Init
  useEffect(() => {
    const initAuth = async () => {
      if (!auth) return;
      try {
        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          await signInAnonymously(auth);
        }
      } catch (e) {
        console.error("Auth sign-in failed:", e);
      }
    };
    initAuth();
    if (auth) {
      const unsubscribe = onAuthStateChanged(auth, (u) => {
        setUser(u);
        setIsAuthReady(true);
      });
      return () => unsubscribe();
    }
  }, []);

  // Sync Public Cloud Tracks (The main music queue)
  useEffect(() => {
    if (!isAuthReady || !db) return;

    const q = query(getPublicTracksPath());
    const unsubscribe = onSnapshot(q, async (snapshot) => {
        const tracks = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
        setQueue(tracks);
        
        // Seed database if empty with demo tracks (Admin-like behavior)
        if (tracks.length === 0 && user) {
            console.log("Seeding public collection with demo tracks...");
            for (const track of DEMO_TRACKS) {
                // Use setDoc with a deterministic ID based on title to prevent duplicates on every run
                await setDoc(doc(getPublicTracksPath(), track.id), {
                    ...track,
                    createdAt: serverTimestamp()
                });
            }
        }
        
        // Set first track if none is playing
        if (!currentTrack && tracks.length > 0) {
            setCurrentTrack(tracks[0]);
        }
    }, (error) => console.error("Public Tracks sync error:", error));

    return () => unsubscribe();
  }, [isAuthReady, user]); // Depend on user to handle the seeding logic

  // Fetch Private Playlists (Cloud Sync)
  useEffect(() => {
    if (!user || !db) return;
    const unsubscribe = onSnapshot(getUserPlaylistPath(user.uid), 
      (snapshot) => {
        const list = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
        setPlaylists(list);
      },
      (error) => console.error("Playlist sync error:", error)
    );
    return () => unsubscribe();
  }, [user]);

  // Load Local Library (Offline)
  useEffect(() => {
    loadLocalLibrary();
    // Cleanup old object URLs on unmount
    return () => {
        localLibrary.forEach(t => {
            if (t.objectUrl) URL.revokeObjectURL(t.objectUrl);
        });
    };
  }, []);

  const loadLocalLibrary = async () => {
    try {
      const tracks = await getTracksFromIDB();
      const tracksWithUrls = tracks.map(t => ({
        ...t,
        audioBlob: t.audioBlob, // Keep the blob reference
        objectUrl: URL.createObjectURL(t.audioBlob),
        isOffline: true
      }));
      setLocalLibrary(tracksWithUrls);
    } catch (e) {
      console.error("IDB Load Error:", e);
    }
  };

  // Audio Logic
  useEffect(() => {
    const audio = audioRef.current;
    
    const handleTimeUpdate = () => setCurrentTime(audio.currentTime);
    const handleLoadedMetadata = () => setDuration(audio.duration);
    const handleEnded = () => playNext();

    audio.addEventListener('timeupdate', handleTimeUpdate);
    audio.addEventListener('loadedmetadata', handleLoadedMetadata);
    audio.addEventListener('ended', handleEnded);

    return () => {
      audio.removeEventListener('timeupdate', handleTimeUpdate);
      audio.removeEventListener('loadedmetadata', handleLoadedMetadata);
      audio.removeEventListener('ended', handleEnded);
    };
  }, [playMode, queue, currentTrack]);

  useEffect(() => {
    if (currentTrack) {
      // Prioritize local offline version if available
      const localVersion = localLibrary.find(t => t.id === currentTrack.id && t.isOffline);
      const src = localVersion ? localVersion.objectUrl : currentTrack.audioUrl;
      
      if (audioRef.current.src !== src) {
        audioRef.current.src = src;
        audioRef.current.load();
      }
      
      if (isPlaying) {
        audioRef.current.play().catch(e => console.log("Autoplay prevented", e));
      }
    }
  }, [currentTrack, localLibrary]);

  useEffect(() => {
    if (isPlaying) audioRef.current.play();
    else audioRef.current.pause();
  }, [isPlaying]);

  useEffect(() => {
    audioRef.current.volume = volume;
  }, [volume]);

  // Player Controls
  const togglePlay = () => setIsPlaying(!isPlaying);
  
  const playTrack = (track) => {
    if (currentTrack?.id === track.id) {
      togglePlay();
    } else {
      setCurrentTrack(track);
      setIsPlaying(true);
    }
  };

  const playNext = () => {
    if (!currentTrack || queue.length === 0) return;
    if (playMode === 'one') {
      audioRef.current.currentTime = 0;
      audioRef.current.play();
      return;
    }
    
    let nextIndex;
    if (playMode === 'shuffle') {
      nextIndex = Math.floor(Math.random() * queue.length);
    } else {
      const currentIndex = queue.findIndex(t => t.id === currentTrack.id);
      nextIndex = (currentIndex + 1) % queue.length;
    }
    setCurrentTrack(queue[nextIndex]);
    setIsPlaying(true);
  };

  const playPrev = () => {
    if (!currentTrack || queue.length === 0) return;
    const currentIndex = queue.findIndex(t => t.id === currentTrack.id);
    const prevIndex = (currentIndex - 1 + queue.length) % queue.length;
    setCurrentTrack(queue[prevIndex]);
    setIsPlaying(true);
  };

  const handleSeek = (e) => {
    const seekTime = (e.target.value / 100) * duration;
    audioRef.current.currentTime = seekTime;
    setCurrentTime(seekTime);
  };

  // Feature: Download to Browser / Delete from Browser
  const handleDownloadToggle = async (track, e) => {
    e.stopPropagation();
    if (downloadingId) return;
    
    const exists = localLibrary.find(t => t.id === track.id);
    if (exists) {
      // Delete from local library
      await deleteTrackFromIDB(track.id);
      // Revoke the object URL before reloading
      if (exists.objectUrl) URL.revokeObjectURL(exists.objectUrl);
      await loadLocalLibrary();
      return;
    }

    // Download to local library
    setDownloadingId(track.id);
    try {
      const response = await fetch(track.audioUrl);
      const blob = await response.blob();
      // Use the cloud track's ID for linking
      await saveTrackToIDB(track, blob);
      await loadLocalLibrary();
    } catch (err) {
      console.error("Download failed:", err);
    } finally {
      setDownloadingId(null);
    }
  };

  // Feature: Import Local File (for Offline Library only)
  const handleFileUploadLocal = async (e) => {
    const file = e.target.files[0];
    if (!file) return;

    if (!file.name.toLowerCase().endsWith('.mp3') && !file.name.toLowerCase().endsWith('.wav')) {
        console.warn("Only MP3/WAV files are supported for import.");
        return;
    }

    const newTrack = {
      // Use a unique ID that won't conflict with cloud tracks
      id: `local-${crypto.randomUUID()}`, 
      title: file.name.replace(/\.[^/.]+$/, ""), // remove extension
      artist: 'Local Upload',
      album: 'My Uploads',
      duration: 0,
      genre: 'Custom',
      coverUrl: 'https://images.unsplash.com/photo-1614613535308-eb5fbd3d2c17?w=400&q=80',
      source: 'local',
      isOffline: true
    };

    try {
      await saveTrackToIDB(newTrack, file);
      await loadLocalLibrary();
    } catch (err) {
      console.error("Local Upload failed", err);
    }
  };

  // Feature: Admin Panel Password Check
  const handleAdminLogin = (e) => {
    e.preventDefault();
    const isCorrect = decodeBase64(ADMIN_PASSWORD_HASH) === adminPassword;
    if (isCorrect) {
      setIsAdmin(true);
      setShowSettingsModal(false);
      setAdminPassword('');
      setView('admin');
    } else {
      // Using a custom message box instead of alert for better UI/UX
      console.error("Incorrect password. Access denied.");
      alert("Incorrect password. Access denied."); 
    }
  };

  // Feature: Admin Panel - Add New Track to Cloud
  const AdminTrackForm = () => {
    const [title, setTitle] = useState('');
    const [artist, setArtist] = useState('');
    const [genre, setGenre] = useState('');
    const [coverUrl, setCoverUrl] = useState('');
    const [audioUrl, setAudioUrl] = useState('');
    const [isSubmitting, setIsSubmitting] = useState(false);

    const handleSubmit = async (e) => {
      e.preventDefault();
      if (!title || !artist || !audioUrl) {
        alert("Title, Artist, and Audio URL are required.");
        return;
      }
      setIsSubmitting(true);
      try {
        await addDoc(getPublicTracksPath(), {
          title,
          artist,
          genre: genre || 'Unknown',
          coverUrl: coverUrl || 'https://placehold.co/400x400/1e293b/ffffff?text=No+Cover',
          audioUrl,
          duration: 0, // Duration will be set on playback
          createdAt: serverTimestamp()
        });
        
        // Clear form
        setTitle(''); setArtist(''); setGenre(''); setCoverUrl(''); setAudioUrl('');
        alert("Track added to public cloud successfully!");
      } catch (error) {
        console.error("Error adding document: ", error);
        alert(`Failed to add track: ${error.message}`);
      } finally {
        setIsSubmitting(false);
      }
    };

    return (
      <div className="bg-gray-800/70 p-6 rounded-xl border border-gray-700">
        <h3 className="text-xl font-bold mb-4 flex items-center gap-2 text-purple-400"><Plus size={20} /> Add New Public Track</h3>
        <form onSubmit={handleSubmit} className="space-y-4">
          <input type="text" placeholder="Title (Required)" value={title} onChange={(e) => setTitle(e.target.value)} className="w-full bg-gray-900 border border-gray-700 rounded p-3" />
          <input type="text" placeholder="Artist (Required)" value={artist} onChange={(e) => setArtist(e.target.value)} className="w-full bg-gray-900 border border-gray-700 rounded p-3" />
          <input type="text" placeholder="Genre (Optional)" value={genre} onChange={(e) => setGenre(e.target.value)} className="w-full bg-gray-900 border border-gray-700 rounded p-3" />
          <input type="url" placeholder="Cover Image URL (Optional)" value={coverUrl} onChange={(e) => setCoverUrl(e.target.value)} className="w-full bg-gray-900 border border-gray-700 rounded p-3" />
          <input type="url" placeholder="Audio File URL (MP3/WAV - Required)" value={audioUrl} onChange={(e) => setAudioUrl(e.target.value)} className="w-full bg-gray-900 border border-gray-700 rounded p-3" />
          
          <button type="submit" disabled={isSubmitting} className="w-full py-3 bg-purple-600 hover:bg-purple-500 rounded-lg font-bold transition-colors disabled:opacity-50">
            {isSubmitting ? 'Adding...' : 'Add Track to Cloud Stream'}
          </button>
        </form>
      </div>
    );
  };

  // Feature: LLM Analyzer - Modal Trigger
  const openAnalyzerModal = async (track) => {
    setCurrentTrack(track);
    setShowAnalyzerModal(true);
    setLlmAnalysis('Loading analysis from Gemini...');
    
    const analysis = await analyzeTrackWithGemini(track);
    setLlmAnalysis(analysis);
  };

  // --- RENDER HELPERS ---
  const formatTime = (time) => {
    if (!time) return "0:00";
    const min = Math.floor(time / 60);
    const sec = Math.floor(time % 60);
    return `${min}:${sec < 10 ? '0' + sec : sec}`;
  };

  const isDownloaded = (id) => localLibrary.some(t => t.id === id);
  const currentView = isAdmin && view === 'admin' ? 'admin' : view;
  
  return (
    <div className="h-screen w-screen bg-gray-950 text-white flex flex-col overflow-hidden font-sans selection:bg-purple-500 selection:text-white">
      
      {/* --- TOP BAR (Mobile & Settings) --- */}
      <div className="flex items-center justify-between p-4 bg-gray-900 border-b border-gray-800">
        <div className="flex items-center gap-2 font-bold text-xl tracking-tighter text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-600">
          <Music className="text-purple-500" /> SonicStream
        </div>
        <div className="flex items-center gap-3">
            <button onClick={() => setShowSettingsModal(true)} className="p-2 rounded-full hover:bg-gray-700 hidden sm:block">
                <Settings size={20} />
            </button>
            <button onClick={() => setSidebarOpen(!sidebarOpen)} className="sm:hidden">
              {sidebarOpen ? <X /> : <Menu />}
            </button>
        </div>
      </div>

      <div className="flex flex-1 overflow-hidden">
        
        {/* --- SIDEBAR --- */}
        <aside className={`
          fixed md:relative z-20 h-full w-64 bg-gray-900 border-r border-gray-800 flex flex-col transition-transform duration-300
          ${sidebarOpen ? 'translate-x-0' : '-translate-x-full md:translate-x-0'}
        `}>
          <div className="p-6 hidden md:flex items-center gap-2 font-bold text-2xl tracking-tighter">
            <div className="w-8 h-8 bg-gradient-to-br from-purple-500 to-pink-500 rounded-lg flex items-center justify-center">
              <Music size={18} className="text-white" />
            </div>
            <span>SonicStream</span>
          </div>

          <nav className="flex-1 px-4 py-4 space-y-1">
            <button 
              onClick={() => { setView('stream'); setSidebarOpen(false); }} 
              className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition-all ${currentView === 'stream' ? 'bg-purple-600/20 text-purple-400 font-medium' : 'hover:bg-gray-800 text-gray-400'}`}
            >
              <Cloud size={20} /> Cloud Stream
            </button>
            <button 
              onClick={() => { setView('library'); setSidebarOpen(false); }} 
              className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition-all ${currentView === 'library' ? 'bg-purple-600/20 text-purple-400 font-medium' : 'hover:bg-gray-800 text-gray-400'}`}
            >
              <HardDrive size={20} /> Offline Library
            </button>
            
            {isAdmin && (
                <button 
                  onClick={() => { setView('admin'); setSidebarOpen(false); }} 
                  className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl transition-all ${currentView === 'admin' ? 'bg-pink-600/20 text-pink-400 font-medium' : 'hover:bg-gray-800 text-gray-400'}`}
                >
                  <Lock size={20} /> Admin Panel
                </button>
            )}

            <div className="pt-6 pb-2 px-4 text-xs font-semibold text-gray-500 uppercase tracking-wider">
              Playlists
            </div>
            {/* Playlist rendering code (omitted for brevity, assume similar to original) */}
            <div className="space-y-1 overflow-y-auto max-h-64">
              {playlists.map(pl => (
                <div key={pl.id} className="group flex items-center justify-between px-4 py-2 text-sm text-gray-400 hover:text-white rounded-lg hover:bg-gray-800 cursor-pointer">
                  <span className="truncate">{pl.name}</span>
                  {/* Delete logic needed */}
                  <Trash2 size={14} className="opacity-0 group-hover:opacity-100 text-red-400" />
                </div>
              ))}
              <button className="w-full flex items-center gap-2 px-4 py-2 text-sm text-gray-500 hover:text-white transition-colors">
                <Plus size={16} /> New Playlist
              </button>
            </div>
          </nav>
          
          <div className="p-4 border-t border-gray-800">
            <div className="bg-gray-800/50 rounded-xl p-3 flex items-center gap-3">
              <div className="w-8 h-8 rounded-full bg-gradient-to-tr from-blue-400 to-purple-500 flex items-center justify-center text-xs font-bold">
                {user ? user.uid.substring(0, 2).toUpperCase() : 'G'}
              </div>
              <div className="flex-1 overflow-hidden">
                <div className="text-xs font-medium truncate">
                  {user ? `User ${user.uid.substring(0, 4)}...` : 'Guest'}
                </div>
                <div className={`text-[10px] flex items-center gap-1 ${isAdmin ? 'text-pink-400' : 'text-green-400'}`}>
                  <div className={`w-1.5 h-1.5 rounded-full ${isAdmin ? 'bg-pink-500' : 'bg-green-500'} animate-pulse`}></div>
                  {isAdmin ? 'Admin Mode' : 'Online Sync'}
                </div>
              </div>
            </div>
          </div>
        </aside>

        {/* --- MAIN CONTENT AREA --- */}
        <main className="flex-1 bg-gray-950 overflow-y-auto relative">
          <div className="absolute top-0 left-0 w-full h-64 bg-gradient-to-b from-purple-900/20 to-transparent pointer-events-none" />

          <div className="relative p-6 md:p-10 max-w-7xl mx-auto pb-32">
            
            {/* VIEW: STREAM (Public Cloud Tracks) */}
            {currentView === 'stream' && (
              <>
                <h1 className="text-3xl font-bold mb-6 flex items-center gap-2"><Cloud className="text-purple-400" size={28} /> Shared Cloud Stream</h1>
                <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                  {queue.map(track => (
                    <div 
                      key={track.id} 
                      onClick={() => playTrack(track)}
                      className={`
                        group relative bg-gray-900/60 border border-gray-800 hover:border-purple-500/50 rounded-xl p-4 flex gap-4 transition-all hover:bg-gray-800 cursor-pointer
                        ${currentTrack?.id === track.id ? 'border-purple-500 bg-purple-500/10' : ''}
                      `}
                    >
                      <div className="relative w-20 h-20 flex-shrink-0">
                        <img src={track.coverUrl} alt={track.title} className="w-full h-full object-cover rounded-lg shadow-lg" />
                        <div className={`absolute inset-0 bg-black/40 flex items-center justify-center rounded-lg transition-opacity ${currentTrack?.id === track.id && isPlaying ? 'opacity-100' : 'opacity-0 group-hover:opacity-100'}`}>
                          {currentTrack?.id === track.id && isPlaying ? <Pause className="fill-white text-white" size={24} /> : <Play className="fill-white text-white" size={24} />}
                        </div>
                      </div>
                      <div className="flex-1 min-w-0 flex flex-col justify-center">
                        <h3 className={`font-bold truncate ${currentTrack?.id === track.id ? 'text-purple-400' : 'text-white'}`}>{track.title}</h3>
                        <p className="text-sm text-gray-400 truncate">{track.artist}</p>
                        <div className="flex items-center gap-2 mt-2">
                          <span className="text-xs px-2 py-0.5 rounded-full bg-gray-800 text-gray-400 border border-gray-700">{track.genre}</span>
                        </div>
                      </div>
                      <div className="flex flex-col justify-between items-end">
                        <button 
                          onClick={(e) => handleDownloadToggle(track, e)}
                          className={`p-2 rounded-full transition-colors`}
                          title={isDownloaded(track.id) ? "Delete Offline Copy" : "Download Offline"}
                        >
                          {downloadingId === track.id ? (
                            <div className="w-4 h-4 border-2 border-current border-t-transparent rounded-full animate-spin text-purple-400" />
                          ) : isDownloaded(track.id) ? (
                            <Trash2 size={18} className="text-red-400 hover:bg-red-400/10" />
                          ) : (
                            <Download size={18} className="text-gray-500 hover:text-white hover:bg-gray-700 rounded-full" />
                          )}
                        </button>
                        <button 
                          onClick={(e) => { e.stopPropagation(); openAnalyzerModal(track); }}
                          className="p-2 text-gray-500 hover:text-purple-400 hover:bg-gray-700 rounded-full"
                          title="AI Analyze"
                        >
                          <Sparkles size={16} />
                        </button>
                      </div>
                    </div>
                  ))}
                </div>
              </>
            )}

            {/* VIEW: LIBRARY (Offline Local Tracks) */}
            {currentView === 'library' && (
              <>
                <div className="flex items-center justify-between mb-6">
                  <h1 className="text-3xl font-bold flex items-center gap-2"><HardDrive className="text-green-400" size={28} /> Offline Library</h1>
                  
                  {/* Local Upload Button for Offline Library */}
                  <div>
                    <input 
                      type="file" 
                      ref={fileInputRef} 
                      onChange={handleFileUploadLocal} 
                      accept="audio/*" 
                      className="hidden" 
                    />
                    <button 
                      onClick={() => fileInputRef.current?.click()}
                      className="flex items-center gap-2 px-4 py-2 bg-green-600 hover:bg-green-500 text-white rounded-full font-bold text-sm transition-colors shadow-lg shadow-green-900/50"
                    >
                      <Upload size={16} /> Import MP3
                    </button>
                  </div>
                </div>
                
                {localLibrary.length === 0 ? (
                  <div className="flex flex-col items-center justify-center py-20 text-gray-500">
                    <Library size={48} className="mb-4 opacity-50" />
                    <p className="text-lg font-medium">Your browser library is empty</p>
                    <p className="text-sm">Import local MP3s or download from the Cloud Stream view.</p>
                  </div>
                ) : (
                  <div className="grid grid-cols-1 gap-2">
                    {localLibrary.map(track => (
                      <div 
                        key={track.id} 
                        onClick={() => playTrack(track)}
                        className={`
                          flex items-center gap-4 p-3 rounded-xl hover:bg-gray-800 cursor-pointer border border-transparent
                          ${currentTrack?.id === track.id ? 'bg-gray-800 border-gray-700' : ''}
                        `}
                      >
                        <img src={track.coverUrl} className="w-12 h-12 rounded bg-gray-700 object-cover" />
                        <div className="flex-1">
                          <h4 className={`font-medium ${currentTrack?.id === track.id ? 'text-green-400' : 'text-white'}`}>{track.title}</h4>
                          <p className="text-xs text-gray-400">{track.artist} • {track.album}</p>
                        </div>
                        <div className="text-xs text-gray-500">
                          {/* Display file size if available */}
                          {track.audioBlob ? `${(track.audioBlob.size / 1024 / 1024).toFixed(1)} MB` : ''}
                        </div>
                        <button 
                          onClick={(e) => { e.stopPropagation(); deleteTrackFromIDB(track.id).then(loadLocalLibrary); }}
                          className="p-2 text-red-400 hover:bg-red-400/10 rounded-full"
                          title="Permanently Delete Local File"
                        >
                          <Trash2 size={16} />
                        </button>
                      </div>
                    ))}
                  </div>
                )}
              </>
            )}

            {/* VIEW: ADMIN PANEL */}
            {currentView === 'admin' && isAdmin && (
                <>
                    <h1 className="text-3xl font-bold mb-6 flex items-center gap-2 text-pink-400"><Lock size={28} /> Administrator Panel</h1>
                    <div className="mb-8">
                       <AdminTrackForm />
                    </div>
                    <div className="p-6 bg-gray-900 rounded-xl border border-gray-700">
                      <h2 className="text-xl font-bold mb-4 text-white">Public Track Management ({queue.length})</h2>
                      <p className="text-sm text-gray-400 mb-4">
                        These tracks are visible to all users. Changes take effect immediately.
                      </p>
                      <div className="space-y-2">
                        {queue.map(track => (
                          <div key={track.id} className="flex items-center justify-between p-3 bg-gray-950 rounded-lg border border-gray-800">
                            <div className="flex items-center gap-3">
                                <img src={track.coverUrl} className="w-10 h-10 rounded object-cover" />
                                <div className="text-sm font-medium">{track.title} <span className="text-gray-500 text-xs">by {track.artist}</span></div>
                            </div>
                            <button 
                                onClick={async () => { 
                                    if (window.confirm(`Delete ${track.title}?`)) {
                                        await deleteDoc(doc(getPublicTracksPath(), track.id)); 
                                    }
                                }}
                                className="p-2 text-red-500 hover:bg-red-500/10 rounded-full"
                            >
                                <Trash2 size={16} />
                            </button>
                          </div>
                        ))}
                      </div>
                    </div>
                </>
            )}
            
          </div>
        </main>
      </div>

      {/* --- PLAYER BAR --- */}
      <div className="h-24 bg-gray-900 border-t border-gray-800 px-4 md:px-8 flex items-center justify-between z-30 relative">
        
        {/* Track Info */}
        <div className="flex items-center gap-4 w-1/3 min-w-0">
          {currentTrack ? (
            <>
               <div className="relative group w-14 h-14">
                 <img src={currentTrack.coverUrl} className="w-full h-full rounded shadow-md object-cover" onError={(e) => { e.target.onerror = null; e.target.src="https://placehold.co/400x400/1e293b/ffffff?text=No+Cover" }} />
               </div>
               <div className="min-w-0">
                 <h4 className="font-bold text-sm truncate text-white">{currentTrack.title}</h4>
                 <p className="text-xs text-gray-400 truncate">{currentTrack.artist}</p>
                 {localLibrary.some(t => t.id === currentTrack.id) && (
                   <span className="text-[10px] text-green-400 flex items-center gap-0.5 mt-0.5"><HardDrive size={8}/> Offline Copy</span>
                 )}
               </div>
            </>
          ) : (
            <div className="text-gray-500 text-sm">Select a track to play</div>
          )}
        </div>

        {/* Controls */}
        <div className="flex flex-col items-center w-1/3">
          <div className="flex items-center gap-6 mb-2">
             <button 
              onClick={() => setPlayMode(m => m === 'shuffle' ? 'all' : 'shuffle')}
              className={`${playMode === 'shuffle' ? 'text-purple-400' : 'text-gray-500 hover:text-white'}`}
            >
               <Shuffle size={18} />
             </button>
             <button onClick={playPrev} className="text-white hover:text-purple-400 transition-colors disabled:opacity-50" disabled={queue.length === 0}><SkipBack size={24} className="fill-current" /></button>
             <button onClick={togglePlay} disabled={!currentTrack} className="w-10 h-10 bg-white rounded-full flex items-center justify-center hover:scale-105 transition-transform text-black disabled:bg-gray-600 disabled:cursor-not-allowed">
               {isPlaying ? <Pause size={20} className="fill-current" /> : <Play size={20} className="fill-current translate-x-0.5" />}
             </button>
             <button onClick={playNext} className="text-white hover:text-purple-400 transition-colors disabled:opacity-50" disabled={queue.length === 0}><SkipForward size={24} className="fill-current" /></button>
             <button 
              onClick={() => setPlayMode(m => m === 'one' ? 'all' : 'one')}
              className={`${playMode === 'one' ? 'text-purple-400' : 'text-gray-500 hover:text-white'}`}
            >
               <Repeat size={18} />
             </button>
          </div>
          <div className="w-full max-w-md flex items-center gap-3 text-xs text-gray-400 font-mono">
             <span>{formatTime(currentTime)}</span>
             <input 
               type="range" 
               min={0} 
               max={100} 
               value={duration ? (currentTime / duration) * 100 : 0} 
               onChange={handleSeek}
               className="flex-1 h-1 bg-gray-700 rounded-lg appearance-none cursor-pointer [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:w-3 [&::-webkit-slider-thumb]:h-3 [&::-webkit-slider-thumb]:bg-purple-400 [&::-webkit-slider-thumb]:rounded-full"
             />
             <span>{formatTime(duration)}</span>
          </div>
        </div>

        {/* Volume & Extras */}
        <div className="flex items-center justify-end gap-3 w-1/3">
           <Music size={18} className="text-gray-500" />
           <input 
             type="range" 
             min={0} 
             max={1} 
             step={0.01}
             value={volume}
             onChange={(e) => setVolume(parseFloat(e.target.value))}
             className="w-24 h-1 bg-gray-700 rounded-lg appearance-none cursor-pointer [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:w-3 [&::-webkit-slider-thumb]:h-3 [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:rounded-full"
           />
        </div>
      </div>
      
      {/* --- SETTINGS/ADMIN MODAL --- */}
      {showSettingsModal && (
        <div className="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4">
            <div className="bg-gray-900 border border-gray-700 rounded-2xl w-full max-w-sm overflow-hidden shadow-2xl">
                <div className="p-6 border-b border-gray-800 flex justify-between items-center">
                    <h3 className="font-bold text-xl flex items-center gap-2"><Settings className="text-gray-400" size={20} /> Settings</h3>
                    <button onClick={() => setShowSettingsModal(false)}><X /></button>
                </div>
                
                <div className="p-6 space-y-4">
                    <h4 className="font-bold text-lg text-white">Admin Access</h4>
                    <p className={`text-sm flex items-center gap-2 ${isAdmin ? 'text-pink-400' : 'text-gray-400'}`}>
                        <Key size={14} /> Status: {isAdmin ? 'Logged in as Admin' : 'Not logged in'}
                    </p>
                    
                    {!isAdmin ? (
                        <form onSubmit={handleAdminLogin} className="space-y-3">
                            <input 
                                type="password" 
                                placeholder="Enter Admin Password" 
                                value={adminPassword} 
                                onChange={(e) => setAdminPassword(e.target.value)} 
                                className="w-full bg-gray-800 border border-gray-700 rounded px-3 py-2 mt-1 focus:outline-none" 
                            />
                            <button type="submit" className="w-full py-2 bg-pink-600 hover:bg-pink-500 rounded-lg text-sm font-bold transition-colors flex items-center justify-center gap-2">
                                <Lock size={14} /> Unlock Admin Panel
                            </button>
                        </form>
                    ) : (
                        <button onClick={() => { setIsAdmin(false); setShowSettingsModal(false); }} className="w-full py-2 bg-gray-700 hover:bg-gray-600 rounded-lg text-sm font-bold transition-colors">
                            Logout Admin
                        </button>
                    )}
                </div>
                
                <div className="bg-gray-950 p-4 text-xs text-gray-500 text-center">
                    User ID: {user?.uid || 'N/A'}
                </div>
            </div>
        </div>
      )}

      {/* --- GEMINI ANALYZER MODAL --- */}
      {showAnalyzerModal && currentTrack && (
        <div className="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4">
          <div className="bg-gray-900 border border-gray-700 rounded-2xl w-full max-w-lg overflow-hidden shadow-2xl">
            <div className="p-6 border-b border-gray-800 flex justify-between items-center">
              <h3 className="font-bold text-xl flex items-center gap-2">
                <Sparkles className="text-blue-400" size={20} /> Gemini Track Analyzer
              </h3>
              <button onClick={() => setShowAnalyzerModal(false)}><X /></button>
            </div>
            
            <div className="p-6 flex gap-6">
              <div className="w-32 h-32 flex-shrink-0">
                <img src={currentTrack.coverUrl} className="w-full h-full rounded-xl object-cover border border-gray-700" />
              </div>

              <div className="flex-1 space-y-4">
                <h4 className="text-xl font-bold text-white">{currentTrack.title}</h4>
                <p className="text-sm text-gray-400">Artist: <span className="text-white">{currentTrack.artist}</span></p>
                <div className="flex gap-2">
                    <span className="px-3 py-1 bg-purple-500/20 text-purple-300 border border-purple-500/30 rounded-full text-xs">{currentTrack.genre}</span>
                </div>
                
                <div className="mt-4 pt-4 border-t border-gray-800">
                    <p className="text-xs text-blue-400 font-bold uppercase mb-2 flex items-center gap-1">
                        <Sparkles size={12} /> AI Analysis
                    </p>
                    {llmAnalysis.startsWith("Loading") ? (
                        <div className="flex items-center gap-2 text-gray-400">
                            <div className="w-4 h-4 border-2 border-blue-400 border-t-transparent rounded-full animate-spin"></div>
                            <p className="text-sm">{llmAnalysis}</p>
                        </div>
                    ) : llmAnalysis.startsWith("Error") ? (
                         <div className="flex items-center gap-2 text-red-400">
                            <AlertTriangle size={16} />
                            <p className="text-sm">{llmAnalysis}</p>
                        </div>
                    ) : (
                        <p className="text-sm text-gray-300 italic leading-relaxed">{llmAnalysis}</p>
                    )}
                </div>
              </div>
            </div>
            
            <div className="bg-gray-950 p-4 text-xs text-gray-500 flex justify-between items-center">
               <span></span>
               <button onClick={() => setShowAnalyzerModal(false)} className="hover:text-white">Close</button>
            </div>
          </div>
        </div>
      )}

    </div>
  );
}
