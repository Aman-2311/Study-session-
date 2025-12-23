import React, { useState, useEffect, useRef } from 'react';
import { Play, Pause, RotateCcw, Award, Coffee, BookOpen, Zap, Target, Brain, Star, Trophy, Music, Volume2, VolumeX } from 'lucide-react';

const EpicStudyBuddy = () => {
  const [currentPage, setCurrentPage] = useState('home');
  const [studyTime, setStudyTime] = useState(25 * 60);
  const [currentTime, setCurrentTime] = useState(25 * 60);
  const [isRunning, setIsRunning] = useState(false);
  const [isBreak, setIsBreak] = useState(false);
  const [sessionsCompleted, setSessionsCompleted] = useState(0);
  const [totalPoints, setTotalPoints] = useState(0);
  const [catMood, setCatMood] = useState('vibing');
  const [showMessage, setShowMessage] = useState(false);
  const [currentMessage, setCurrentMessage] = useState('');
  const [isDancing, setIsDancing] = useState(false);
  const [soundEnabled, setSoundEnabled] = useState(false);
  
  // Game 1: Whack-a-Cat
  const [moles, setMoles] = useState(Array(9).fill(false));
  const [whackScore, setWhackScore] = useState(0);
  const [whackGameActive, setWhackGameActive] = useState(false);
  
  // Game 2: Cat Memory Match
  const [memoryCards, setMemoryCards] = useState([]);
  const [flippedCards, setFlippedCards] = useState([]);
  const [matchedCards, setMatchedCards] = useState([]);
  const [memoryScore, setMemoryScore] = useState(0);
  
  // Game 3: Catch the Fish
  const [fishPosition, setFishPosition] = useState({ x: 50, y: 20 });
  const [catGamePosition, setCatGamePosition] = useState(50);
  const [fishScore, setFishScore] = useState(0);
  const [fishGameActive, setFishGameActive] = useState(false);
  
  const intervalRef = useRef(null);

  const memeQuotes = [
    "I can has study? 📚😺",
    "Much focus, very wow! 🐕",
    "This is fine... everything is fine 🔥☕",
    "Stonks! Your brain = 📈",
    "Achievement Unlocked! 🏆",
    "Big brain time! 🧠✨",
    "You're breathtaking! ⭐",
    "It's over 9000! 💪",
    "Task failed successfully! 😹",
    "Upgrades people, upgrades! 🚀",
    "Am I a joke to you? (No, you're amazing!) 👑",
    "Suffering from success 😎",
    "Outstanding move! ♟️",
    "That's what heroes do 🦸",
    "Because that's what legends do 🌟"
  ];

  const breakMemes = [
    "Take a break! You deserve it! ☕😸",
    "Chillin like a villain 😎",
    "Netflix and chill... but study! 📺",
    "Treat yo'self! 🎉",
    "Vibin' check! ✌️😺"
  ];

  const catEmojis = ['😺', '😸', '😹', '😻', '😼', '😽', '🙀', '😿', '😾', '🐱'];

  useEffect(() => {
    const saved = localStorage.getItem('epicStudyData');
    if (saved) {
      const data = JSON.parse(saved);
      setSessionsCompleted(data.sessions || 0);
      setTotalPoints(data.points || 0);
    }
  }, []);

  useEffect(() => {
    if (isRunning) {
      intervalRef.current = setInterval(() => {
        setCurrentTime(prev => {
          if (prev <= 1) {
            handleTimerComplete();
            return 0;
          }
          return prev - 1;
        });
      }, 1000);
    } else {
      if (intervalRef.current) clearInterval(intervalRef.current);
    }
    return () => {
      if (intervalRef.current) clearInterval(intervalRef.current);
    };
  }, [isRunning]);

  const playSound = (type) => {
    if (!soundEnabled) return;
    const audio = new Audio();
    if (type === 'success') {
      audio.src = 'data:audio/wav;base64,UklGRnoGAABXQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YQoGAACBhYqFbF1fdJivrJBhNjVgodDbq2EcBj+a2/LDciUFLIHO8tiJNwgZaLvt559NEAxQp+PwtmMcBjiR1/LMeSwFJHfH8N2QQAoUXrTp66hVFApGn+DyvmwhBSyBzvLZiTYIGWi779+fTRAMUKfj8LZjHAY4ktfyz3otBSR3x/DdkEALFF+16OynVRQKRp/g8r5sIQUsgc7y2Ik2CBlou+/fn00QDFCn4/C2YxwGOJLX8s96LQUkd8fw3ZBACxRftejsp1UUCkaf4PK+bCEFLIHO8tmJNggZaLvv359NEAxQp+PwtmMcBjiS1/LPei0FJHfH8N2QQAsUX7Xo7KdVFApGn+DyvmwhBSyBzvLZiTYIGWi779+fTRAMUKfj8LZjHAY4ktfyz3otBSR3x/DdkEALFF+16OynVRQKRp/g8r5sIQU=';
    }
    audio.play().catch(() => {});
  };

  const handleTimerComplete = () => {
    setIsRunning(false);
    if (!isBreak) {
      const newSessions = sessionsCompleted + 1;
      const newPoints = totalPoints + 100;
      setSessionsCompleted(newSessions);
      setTotalPoints(newPoints);
      localStorage.setItem('epicStudyData', JSON.stringify({
        sessions: newSessions,
        points: newPoints
      }));
      setCatMood('celebrating');
      showRandomMessage(memeQuotes);
      playSound('success');
      triggerDance();
      setIsBreak(true);
      setCurrentTime(5 * 60);
    } else {
      showRandomMessage(["Back to the grind! Let's gooo! 🚀"]);
      setCurrentTime(studyTime);
      setIsBreak(false);
      setCatMood('vibing');
    }
  };

  const triggerDance = () => {
    setIsDancing(true);
    setTimeout(() => setIsDancing(false), 3000);
  };

  const showRandomMessage = (quotes) => {
    setCurrentMessage(quotes[Math.floor(Math.random() * quotes.length)]);
    setShowMessage(true);
    setTimeout(() => setShowMessage(false), 3000);
  };

  const toggleTimer = () => setIsRunning(!isRunning);

  const resetTimer = () => {
    setIsRunning(false);
    setCurrentTime(isBreak ? 5 * 60 : studyTime);
  };

  const formatTime = (seconds) => {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
  };

  const handleCatClick = () => {
    showRandomMessage(memeQuotes);
    triggerDance();
    playSound('success');
  };

  // Game 1: Whack-a-Cat
  const startWhackGame = () => {
    setWhackGameActive(true);
    setWhackScore(0);
    const gameInterval = setInterval(() => {
      const newMoles = Array(9).fill(false);
      const randomIndex = Math.floor(Math.random() * 9);
      newMoles[randomIndex] = true;
      setMoles(newMoles);
    }, 800);
    
    setTimeout(() => {
      clearInterval(gameInterval);
      setWhackGameActive(false);
      setMoles(Array(9).fill(false));
      const points = whackScore * 10;
      setTotalPoints(prev => prev + points);
      showRandomMessage([`Game Over! +${points} points! 🎮`]);
    }, 20000);
  };

  const whackMole = (index) => {
    if (moles[index]) {
      setWhackScore(prev => prev + 1);
      playSound('success');
      const newMoles = [...moles];
      newMoles[index] = false;
      setMoles(newMoles);
    }
  };

  // Game 2: Memory Match
  const startMemoryGame = () => {
    const emojis = ['😺', '😸', '😹', '😻', '😼', '😽', '🐱', '🐈'];
    const cardPairs = [...emojis, ...emojis]
      .sort(() => Math.random() - 0.5)
      .map((emoji, index) => ({ id: index, emoji, flipped: false }));
    setMemoryCards(cardPairs);
    setFlippedCards([]);
    setMatchedCards([]);
    setMemoryScore(0);
  };

  const flipCard = (index) => {
    if (flippedCards.length === 2 || flippedCards.includes(index) || matchedCards.includes(index)) return;
    
    const newFlipped = [...flippedCards, index];
    setFlippedCards(newFlipped);
    
    if (newFlipped.length === 2) {
      const [first, second] = newFlipped;
      if (memoryCards[first].emoji === memoryCards[second].emoji) {
        setMatchedCards(prev => [...prev, first, second]);
        setMemoryScore(prev => prev + 20);
        setTotalPoints(prev => prev + 20);
        playSound('success');
        setFlippedCards([]);
      } else {
        setTimeout(() => setFlippedCards([]), 1000);
      }
    }
  };

  // Game 3: Catch the Fish
  const startFishGame = () => {
    setFishGameActive(true);
    setFishScore(0);
    const gameInterval = setInterval(() => {
      setFishPosition({
        x: Math.random() * 80 + 10,
        y: Math.random() * 60 + 10
      });
    }, 1500);
    
    setTimeout(() => {
      clearInterval(gameInterval);
      setFishGameActive(false);
      const points = fishScore * 15;
      setTotalPoints(prev => prev + points);
      showRandomMessage([`Caught ${fishScore} fish! +${points} points! 🐟`]);
    }, 20000);
  };

  const moveCatGame = (direction) => {
    setCatGamePosition(prev => {
      const newPos = direction === 'left' ? Math.max(0, prev - 15) : Math.min(100, prev + 15);
      
      if (Math.abs(newPos - fishPosition.x) < 10 && fishPosition.y > 50) {
        setFishScore(prev => prev + 1);
        playSound('success');
        setFishPosition({ x: Math.random() * 80 + 10, y: 10 });
      }
      return newPos;
    });
  };

  const getCatDisplay = () => {
    if (catMood === 'celebrating') return '🎉😻🎉';
    if (isDancing) return '💃😺🕺';
    return '😺';
  };

  // Home Page
  const HomePage = () => (
    <div className="space-y-6">
      <div className="text-center mb-8">
        <div 
          onClick={handleCatClick}
          className={`text-9xl cursor-pointer inline-block transition-all duration-300
            ${isDancing ? 'animate-bounce' : 'hover:scale-110'}`}
        >
          {getCatDisplay()}
        </div>
        {showMessage && (
          <div className="mt-4 bg-white rounded-2xl px-6 py-3 shadow-lg border-4 border-purple-400
            animate-pulse inline-block">
            <p className="text-xl font-bold text-purple-600">{currentMessage}</p>
          </div>
        )}
      </div>

      <div className="bg-white rounded-3xl shadow-2xl p-8">
        <div className="text-center mb-6">
          <div className="text-7xl font-bold text-purple-600 mb-4">
            {formatTime(currentTime)}
          </div>
          <p className="text-2xl font-bold text-gray-700">
            {isBreak ? '☕ Break Time - Play Games!' : '📖 Focus Time!'}
          </p>
          <div className="w-full bg-gray-200 rounded-full h-4 overflow-hidden mt-4">
            <div 
              className={`h-full transition-all duration-1000 ${
                isBreak ? 'bg-green-400' : 'bg-purple-500'
              }`}
              style={{ 
                width: `${((isBreak ? 300 : studyTime) - currentTime) / 
                  (isBreak ? 300 : studyTime) * 100}%` 
              }}
            />
          </div>
        </div>

        <div className="flex justify-center gap-4 mb-6">
          <button
            onClick={toggleTimer}
            className="bg-purple-500 hover:bg-purple-600 text-white rounded-full p-6
              transition-all transform hover:scale-110 shadow-lg"
          >
            {isRunning ? <Pause size={40} /> : <Play size={40} />}
          </button>
          <button
            onClick={resetTimer}
            className="bg-pink-500 hover:bg-pink-600 text-white rounded-full p-6
              transition-all transform hover:scale-110 shadow-lg"
          >
            <RotateCcw size={40} />
          </button>
          <button
            onClick={() => setSoundEnabled(!soundEnabled)}
            className="bg-yellow-500 hover:bg-yellow-600 text-white rounded-full p-6
              transition-all transform hover:scale-110 shadow-lg"
          >
            {soundEnabled ? <Volume2 size={40} /> : <VolumeX size={40} />}
          </button>
        </div>

        <div className="grid grid-cols-3 gap-4">
          <div className="bg-gradient-to-br from-purple-100 to-purple-200 rounded-2xl p-4 text-center">
            <Trophy className="mx-auto mb-2 text-purple-600" size={32} />
            <p className="text-3xl font-bold text-purple-600">{totalPoints}</p>
            <p className="text-sm text-gray-600">Points</p>
          </div>
          <div className="bg-gradient-to-br from-pink-100 to-pink-200 rounded-2xl p-4 text-center">
            <Star className="mx-auto mb-2 text-pink-600" size={32} />
            <p className="text-3xl font-bold text-pink-600">{sessionsCompleted}</p>
            <p className="text-sm text-gray-600">Sessions</p>
          </div>
          <div className="bg-gradient-to-br from-yellow-100 to-yellow-200 rounded-2xl p-4 text-center">
            <Zap className="mx-auto mb-2 text-yellow-600" size={32} />
            <p className="text-3xl font-bold text-yellow-600">{Math.floor(sessionsCompleted / 4)}</p>
            <p className="text-sm text-gray-600">Streak</p>
          </div>
        </div>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
        <button
          onClick={() => setCurrentPage('whack')}
          className="bg-gradient-to-r from-red-400 to-pink-500 text-white rounded-2xl p-6
            transform hover:scale-105 transition-all shadow-lg"
        >
          <Target size={48} className="mx-auto mb-2" />
          <h3 className="text-2xl font-bold mb-2">Whack-a-Cat</h3>
          <p className="text-sm">Test your reflexes!</p>
        </button>
        <button
          onClick={() => { setCurrentPage('memory'); startMemoryGame(); }}
          className="bg-gradient-to-r from-blue-400 to-purple-500 text-white rounded-2xl p-6
            transform hover:scale-105 transition-all shadow-lg"
        >
          <Brain size={48} className="mx-auto mb-2" />
          <h3 className="text-2xl font-bold mb-2">Memory Match</h3>
          <p className="text-sm">Match the cats!</p>
        </button>
        <button
          onClick={() => setCurrentPage('fish')}
          className="bg-gradient-to-r from-green-400 to-teal-500 text-white rounded-2xl p-6
            transform hover:scale-105 transition-all shadow-lg"
        >
          <Coffee size={48} className="mx-auto mb-2" />
          <h3 className="text-2xl font-bold mb-2">Catch Fish</h3>
          <p className="text-sm">Feed the cat!</p>
        </button>
      </div>
    </div>
  );

  // Whack-a-Cat Game
  const WhackPage = () => (
    <div className="bg-white rounded-3xl shadow-2xl p-8">
      <button
        onClick={() => setCurrentPage('home')}
        className="mb-4 bg-gray-400 hover:bg-gray-500 text-white px-6 py-2 rounded-full"
      >
        ← Back
      </button>
      <h2 className="text-4xl font-bold text-center text-purple-600 mb-4">
        🎯 Whack-a-Cat!
      </h2>
      <p className="text-center text-xl mb-4">Score: {whackScore}</p>
      
      {!whackGameActive ? (
        <button
          onClick={startWhackGame}
          className="w-full bg-gradient-to-r from-red-500 to-pink-500 text-white py-6 rounded-2xl
            text-2xl font-bold hover:scale-105 transition-all"
        >
          Start Game! (20s)
        </button>
      ) : (
        <div className="grid grid-cols-3 gap-4 max-w-md mx-auto">
          {moles.map((isActive, index) => (
            <button
              key={index}
              onClick={() => whackMole(index)}
              className={`aspect-square rounded-2xl text-6xl flex items-center justify-center
                transition-all transform ${
                isActive 
                  ? 'bg-gradient-to-br from-yellow-300 to-orange-400 scale-110' 
                  : 'bg-gray-200'
              }`}
            >
              {isActive ? '😺' : '🕳️'}
            </button>
          ))}
        </div>
      )}
    </div>
  );

  // Memory Match Game
  const MemoryPage = () => (
    <div className="bg-white rounded-3xl shadow-2xl p-8">
      <button
        onClick={() => setCurrentPage('home')}
        className="mb-4 bg-gray-400 hover:bg-gray-500 text-white px-6 py-2 rounded-full"
      >
        ← Back
      </button>
      <h2 className="text-4xl font-bold text-center text-blue-600 mb-4">
        🧠 Memory Match
      </h2>
      <p className="text-center text-xl mb-4">Score: {memoryScore}</p>
      
      <div className="grid grid-cols-4 gap-4 max-w-lg mx-auto">
        {memoryCards.map((card, index) => (
          <button
            key={card.id}
            onClick={() => flipCard(index)}
            className={`aspect-square rounded-2xl text-5xl flex items-center justify-center
              transition-all transform hover:scale-105 ${
              flippedCards.includes(index) || matchedCards.includes(index)
                ? 'bg-gradient-to-br from-blue-300 to-purple-400'
                : 'bg-gradient-to-br from-gray-300 to-gray-400'
            }`}
          >
            {flippedCards.includes(index) || matchedCards.includes(index) ? card.emoji : '❓'}
          </button>
        ))}
      </div>
      
      <button
        onClick={startMemoryGame}
        className="w-full mt-6 bg-gradient-to-r from-blue-500 to-purple-500 text-white py-4 rounded-2xl
          text-xl font-bold hover:scale-105 transition-all"
      >
        New Game
      </button>
    </div>
  );

  // Catch Fish Game
  const FishPage = () => (
    <div className="bg-white rounded-3xl shadow-2xl p-8">
      <button
        onClick={() => setCurrentPage('home')}
        className="mb-4 bg-gray-400 hover:bg-gray-500 text-white px-6 py-2 rounded-full"
      >
        ← Back
      </button>
      <h2 className="text-4xl font-bold text-center text-green-600 mb-4">
        🐟 Catch the Fish!
      </h2>
      <p className="text-center text-xl mb-4">Fish Caught: {fishScore}</p>
      
      {!fishGameActive ? (
        <button
          onClick={startFishGame}
          className="w-full bg-gradient-to-r from-green-500 to-teal-500 text-white py-6 rounded-2xl
            text-2xl font-bold hover:scale-105 transition-all"
        >
          Start Fishing! (20s)
        </button>
      ) : (
        <div>
          <div className="relative bg-gradient-to-b from-blue-300 to-blue-500 rounded-2xl h-64 mb-4">
            <div 
              className="absolute text-6xl transition-all duration-300"
              style={{ 
                left: `${fishPosition.x}%`, 
                top: `${fishPosition.y}%`,
                transform: 'translate(-50%, -50%)' 
              }}
            >
              🐟
            </div>
            <div 
              className="absolute bottom-0 text-6xl transition-all duration-200"
              style={{ left: `${catGamePosition}%`, transform: 'translateX(-50%)' }}
            >
              😺
            </div>
          </div>
          <div className="flex justify-center gap-4">
            <button
              onClick={() => moveCatGame('left')}
              className="bg-green-500 hover:bg-green-600 text-white px-12 py-4 rounded-full
                font-bold text-2xl transition-all transform hover:scale-110"
            >
              ← Left
            </button>
            <button
              onClick={() => moveCatGame('right')}
              className="bg-teal-500 hover:bg-teal-600 text-white px-12 py-4 rounded-full
                font-bold text-2xl transition-all transform hover:scale-110"
            >
              Right →
            </button>
          </div>
        </div>
      )}
    </div>
  );

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-400 via-pink-300 to-yellow-300 p-4">
      <div className="max-w-4xl mx-auto">
        <div className="text-center mb-6">
          <h1 className="text-6xl font-bold text-white mb-2 drop-shadow-lg animate-pulse">
            🎮 EPIC STUDY BUDDY 🎮
          </h1>
          <p className="text-white text-2xl font-bold">Study Hard, Play Harder!</p>
        </div>

        {currentPage === 'home' && <HomePage />}
        {currentPage === 'whack' && <WhackPage />}
        {currentPage === 'memory' && <MemoryPage />}
        {currentPage === 'fish' && <FishPage />}

        <div className="mt-6 bg-white rounded-3xl shadow-2xl p-6">
          <h3 className="text-2xl font-bold text-center text-purple-600 mb-4">
            💪 Daily Motivation
          </h3>
          <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
            {['🔥 Day 1', '⭐ Day 2', '💎 Day 3', '👑 Day 4'].map((day, i) => (
              <div
                key={i}
                className={`rounded-2xl p-4 text-center font-bold transition-all ${
                  sessionsCompleted >= (i + 1) * 2
                    ? 'bg-gradient-to-br from-yellow-300 to-orange-400 scale-105'
                    : 'bg-gray-200'
                }`}
              >
                {day}
              </div>
            ))}
          </div>
        </div>
      </div>
    </div>
  );
};

export default EpicStudyBuddy;
