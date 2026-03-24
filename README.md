import React, { useState, useEffect, useRef } from 'react';
import { motion, AnimatePresence, useMotionValue, useTransform } from 'framer-motion';
import { 
  Play, 
  BookOpen, 
  Activity, 
  CheckCircle, 
  AlertTriangle, 
  Award, 
  ChevronRight, 
  ChevronLeft, 
  RotateCcw, 
  Settings, 
  Moon, 
  Sun,
  Syringe,
  FlaskConical,
  Droplet,
  ShieldAlert,
  Timer,
  Info
} from 'lucide-react';

// --- Assets ---
const ASSETS = {
  vial: "https://image.qwenlm.ai/public_source/8a9ee2c8-ef74-44ae-bd09-577a5be607fd/1d32abde8-4f31-4ef6-88bc-cd9ae22b636c.png",
  ivBag: "https://image.qwenlm.ai/public_source/8a9ee2c8-ef74-44ae-bd09-577a5be607fd/1853adf72-099b-4715-85a2-432ce83c4234.png",
  hood: "https://image.qwenlm.ai/public_source/8a9ee2c8-ef74-44ae-bd09-577a5be607fd/185dba40b-d909-453b-a3f4-e1e8b035b946.png",
  ppe: "https://image.qwenlm.ai/public_source/8a9ee2c8-ef74-44ae-bd09-577a5be607fd/1e911a80d-9f3c-4291-bd88-c4e1c53847b7.png",
};

// --- Mock Data ---
const THEORY_STEPS = [
  {
    id: 1,
    title: "PPE & Safety",
    content: "Always wear double gloves, a gown, and eye protection. Chemotherapy agents are hazardous.",
    image: ASSETS.ppe,
    icon: <ShieldAlert className="w-6 h-6 text-blue-500" />
  },
  {
    id: 2,
    title: "Laminar Flow Hood",
    content: "Work inside a Class II Biological Safety Cabinet. Ensure airflow is active before starting.",
    image: ASSETS.hood,
    icon: <Activity className="w-6 h-6 text-blue-500" />
  },
  {
    id: 3,
    title: "Aseptic Technique",
    content: "Clean vial stoppers with 70% alcohol. Do not touch sterile parts of the syringe.",
    image: null, // Using icon instead
    icon: <Droplet className="w-6 h-6 text-blue-500" />
  },
  {
    id: 4,
    title: "Reconstitution",
    content: "Inject air equal to drug volume into vial. Invert and withdraw slowly to avoid bubbles.",
    image: ASSETS.vial,
    icon: <FlaskConical className="w-6 h-6 text-blue-500" />
  },
  {
    id: 5,
    title: "Labeling",
    content: "Label immediately with patient name, drug, dose, time, and preparer initials.",
    image: null,
    icon: <BookOpen className="w-6 h-6 text-blue-500" />
  }
];

const QUIZ_QUESTIONS = [
  {
    id: 1,
    question: "What is the correct order of donning PPE?",
    options: ["Gloves, Gown, Mask", "Gown, Mask, Gloves", "Mask, Gloves, Gown"],
    correct: 1
  },
  {
    id: 2,
    question: "How much air should be injected into the vial?",
    options: ["Equal to drug volume", "Double the drug volume", "No air needed"],
    correct: 0
  },
  {
    id: 3,
    question: "Where should chemotherapy mixing occur?",
    options: ["Open bench", "Class II BSC", "Any clean room"],
    correct: 1
  }
];

// --- Components ---

const Button = ({ children, onClick, variant = 'primary', className = '', disabled = false, size = 'md' }) => {
  const baseStyle = "rounded-xl font-bold transition-all active:scale-95 flex items-center justify-center gap-2 shadow-lg";
  const variants = {
    primary: "bg-blue-600 text-white hover:bg-blue-700 disabled:bg-blue-300",
    secondary: "bg-white text-blue-900 border-2 border-blue-100 hover:bg-blue-50 disabled:bg-gray-100",
    danger: "bg-red-500 text-white hover:bg-red-600",
    success: "bg-green-500 text-white hover:bg-green-600",
    ghost: "bg-transparent text-gray-500 hover:bg-gray-100 shadow-none"
  };
  const sizes = {
    sm: "px-3 py-2 text-sm",
    md: "px-6 py-4 text-base min-h-[56px]", // Touch friendly
    lg: "px-8 py-6 text-lg min-h-[64px] w-full"
  };

  return (
    <motion.button
      whileTap={{ scale: 0.95 }}
      className={`${baseStyle} ${variants[variant]} ${sizes[size]} ${className}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </motion.button>
  );
};

const ProgressBar = ({ progress, color = "bg-blue-500" }) => (
  <div className="w-full bg-gray-200 rounded-full h-3 overflow-hidden">
    <motion.div 
      className={`h-full ${color}`} 
      initial={{ width: 0 }}
      animate={{ width: `${progress}%` }}
      transition={{ duration: 0.5 }}
    />
  </div>
);

// --- Main Application ---

export default function App() {
  const [view, setView] = useState('dashboard'); // dashboard, theory, simulation, assessment, results
  const [darkMode, setDarkMode] = useState(false);
  const [userStats, setUserStats] = useState({
    level: 'Beginner',
    xp: 0,
    badges: [],
    completedModules: 0
  });

  // Toggle Dark Mode
  useEffect(() => {
    if (darkMode) {
      document.documentElement.classList.add('dark');
    } else {
      document.documentElement.classList.remove('dark');
    }
  }, [darkMode]);

  const handleCompleteModule = (score) => {
    setUserStats(prev => ({
      ...prev,
      xp: prev.xp + score,
      completedModules: prev.completedModules + 1,
      level: prev.xp + score > 500 ? 'Intermediate' : prev.xp + score > 1000 ? 'Oncology Pro' : 'Beginner'
    }));
    setView('results');
  };

  return (
    <div className={`min-h-screen transition-colors duration-300 ${darkMode ? 'bg-slate-900 text-white' : 'bg-slate-50 text-slate-900'} font-sans overflow-hidden`}>
      {/* Header */}
      <header className="fixed top-0 w-full z-50 px-4 py-3 flex justify-between items-center bg-white/80 dark:bg-slate-800/80 backdrop-blur-md border-b border-gray-200 dark:border-slate-700">
        <div className="flex items-center gap-2">
          <div className="w-8 h-8 bg-blue-600 rounded-lg flex items-center justify-center text-white font-bold">Rx</div>
          <h1 className="font-bold text-lg tracking-tight">OncoPrep<span className="text-blue-500">Sim</span></h1>
        </div>
        <div className="flex items-center gap-3">
          <div className="hidden sm:flex flex-col items-end mr-2">
            <span className="text-xs text-gray-500 dark:text-gray-400">Level</span>
            <span className="text-sm font-bold text-blue-600 dark:text-blue-400">{userStats.level}</span>
          </div>
          <button 
            onClick={() => setDarkMode(!darkMode)}
            className="p-2 rounded-full bg-gray-100 dark:bg-slate-700 text-gray-600 dark:text-gray-300"
          >
            {darkMode ? <Sun size={20} /> : <Moon size={20} />}
          </button>
        </div>
      </header>

      {/* Main Content Area */}
      <main className="pt-20 pb-24 px-4 h-screen overflow-y-auto">
        <AnimatePresence mode="wait">
          {view === 'dashboard' && (
            <Dashboard 
              key="dashboard" 
              stats={userStats} 
              onStartTheory={() => setView('theory')} 
              onStartSim={() => setView('simulation')} 
              onStartAssessment={() => setView('assessment')}
            />
          )}
          {view === 'theory' && (
            <TheoryModule 
              key="theory" 
              onComplete={() => handleCompleteModule(100)} 
              onBack={() => setView('dashboard')} 
            />
          )}
          {view === 'simulation' && (
            <SimulationModule 
              key="simulation" 
              onComplete={(score) => handleCompleteModule(score)} 
              onBack={() => setView('dashboard')} 
            />
          )}
          {view === 'assessment' && (
            <AssessmentModule 
              key="assessment" 
              onComplete={(score) => handleCompleteModule(score)} 
              onBack={() => setView('dashboard')} 
            />
          )}
          {view === 'results' && (
            <ResultsScreen 
              key="results" 
              score={100} // Simplified for demo
              onHome={() => setView('dashboard')} 
            />
          )}
        </AnimatePresence>
      </main>
    </div>
  );
}

// --- Sub-Views ---

function Dashboard({ stats, onStartTheory, onStartSim, onStartAssessment }) {
  return (
    <motion.div 
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: -20 }}
      className="max-w-md mx-auto space-y-6"
    >
      {/* Welcome Card */}
      <div className="bg-gradient-to-br from-blue-600 to-blue-800 rounded-3xl p-6 text-white shadow-xl relative overflow-hidden">
        <div className="absolute top-0 right-0 w-32 h-32 bg-white/10 rounded-full -mr-10 -mt-10 blur-2xl"></div>
        <h2 className="text-2xl font-bold mb-1">Welcome Back</h2>
        <p className="text-blue-100 mb-6">Ready to master IV mixing?</p>
        
        <div className="bg-white/20 backdrop-blur-sm rounded-xl p-4">
          <div className="flex justify-between text-sm mb-2">
            <span>Overall Progress</span>
            <span className="font-bold">{stats.completedModules * 33}%</span>
          </div>
          <ProgressBar progress={stats.completedModules * 33} color="bg-green-400" />
        </div>
      </div>

      {/* Action Grid */}
      <div className="grid grid-cols-1 gap-4">
        <Button onClick={onStartTheory} size="lg" className="justify-between group">
          <div className="flex items-center gap-4">
            <div className="bg-blue-100 p-3 rounded-xl text-blue-600 group-hover:bg-blue-200 transition-colors">
              <BookOpen size={24} />
            </div>
            <div className="text-left">
              <div className="font-bold text-lg">Theory Review</div>
              <div className="text-sm opacity-80">Learn the protocols</div>
            </div>
          </div>
          <ChevronRight className="opacity-50" />
        </Button>

        <Button onClick={onStartSim} size="lg" className="justify-between group bg-indigo-600 hover:bg-indigo-700">
          <div className="flex items-center gap-4">
            <div className="bg-indigo-100 p-3 rounded-xl text-indigo-600 group-hover:bg-indigo-200 transition-colors">
              <Activity size={24} />
            </div>
            <div className="text-left">
              <div className="font-bold text-lg">Simulation</div>
              <div className="text-sm opacity-80">Practice mixing</div>
            </div>
          </div>
          <ChevronRight className="opacity-50" />
        </Button>

        <Button onClick={onStartAssessment} size="lg" variant="secondary" className="justify-between group">
          <div className="flex items-center gap-4">
            <div className="bg-green-100 p-3 rounded-xl text-green-600 group-hover:bg-green-200 transition-colors">
              <Award size={24} />
            </div>
            <div className="text-left">
              <div className="font-bold text-lg">Assessment</div>
              <div className="text-sm opacity-80">Test your knowledge</div>
            </div>
          </div>
          <ChevronRight className="opacity-50" />
        </Button>
      </div>
    </motion.div>
  );
}

function TheoryModule({ onComplete, onBack }) {
  const [currentIndex, setCurrentIndex] = useState(0);
  const [direction, setDirection] = useState(0);

  const slideVariants = {
    enter: (direction) => ({ x: direction > 0 ? 300 : -300, opacity: 0 }),
    center: { zIndex: 1, x: 0, opacity: 1 },
    exit: (direction) => ({ zIndex: 0, x: direction < 0 ? 300 : -300, opacity: 0 }),
  };

  const swipePower = (offset, velocity) => {
    return Math.abs(offset) * velocity;
  };

  const paginate = (newDirection) => {
    setDirection(newDirection);
    setCurrentIndex(prev => prev + newDirection);
  };

  const isLast = currentIndex === THEORY_STEPS.length - 1;

  return (
    <div className="h-full flex flex-col">
      <div className="flex justify-between items-center mb-4">
        <Button variant="ghost" size="sm" onClick={onBack}><ChevronLeft /> Back</Button>
        <span className="font-bold text-gray-500">Step {currentIndex + 1}/{THEORY_STEPS.length}</span>
      </div>

      <div className="flex-1 relative overflow-hidden">
        <AnimatePresence initial={false} custom={direction}>
          <motion.div
            key={currentIndex}
            custom={direction}
            variants={slideVariants}
            initial="enter"
            animate="center"
            exit="exit"
            transition={{
              x: { type: "spring", stiffness: 300, damping: 30 },
              opacity: { duration: 0.2 }
            }}
            drag="x"
            dragConstraints={{ left: 0, right: 0 }}
            dragElastic={1}
            onDragEnd={(e, { offset, velocity }) => {
              const swipe = swipePower(offset.x, velocity.x);
              if (swipe < -100) paginate(1);
              else if (swipe > 100) paginate(-1);
            }}
            className="absolute w-full h-full"
          >
            <div className="bg-white dark:bg-slate-800 rounded-3xl shadow-lg h-full flex flex-col p-6 border border-gray-100 dark:border-slate-700">
              <div className="flex-1 flex flex-col items-center justify-center text-center space-y-6">
                <div className="w-24 h-24 bg-blue-50 dark:bg-slate-700 rounded-full flex items-center justify-center mb-4">
                  {THEORY_STEPS[currentIndex].icon}
                </div>
                
                {THEORY_STEPS[currentIndex].image && (
                  <img 
                    src={THEORY_STEPS[currentIndex].image} 
                    alt={THEORY_STEPS[currentIndex].title}
                    className="w-full h-48 object-cover rounded-2xl shadow-md"
                  />
                )}

                <div>
                  <h3 className="text-2xl font-bold mb-3 text-slate-900 dark:text-white">{THEORY_STEPS[currentIndex].title}</h3>
                  <p className="text-lg text-slate-600 dark:text-slate-300 leading-relaxed">
                    {THEORY_STEPS[currentIndex].content}
                  </p>
                </div>
              </div>
              
              <div className="mt-8 flex justify-between items-center">
                <Button 
                  variant="secondary" 
                  disabled={currentIndex === 0} 
                  onClick={() => paginate(-1)}
                  size="md"
                >
                  Previous
                </Button>
                
                {isLast ? (
                  <Button onClick={onComplete} variant="success" size="md">
                    Finish Learning <CheckCircle size={20} />
                  </Button>
                ) : (
                  <Button onClick={() => paginate(1)} size="md">
                    Next <ChevronRight size={20} />
                  </Button>
                )}
              </div>
            </div>
          </motion.div>
        </AnimatePresence>
      </div>
    </div>
  );
}

function SimulationModule({ onComplete, onBack }) {
  const [step, setStep] = useState(0); // 0: Select Syringe, 1: Withdraw, 2: Inject, 3: Done
  const [contaminated, setContaminated] = useState(false);
  const [timer, setTimer] = useState(0);
  const [feedback, setFeedback] = useState(null); // { type: 'success' | 'error', message: string }
  const [showGuidance, setShowGuidance] = useState(true);

  // Simulation State
  const [syringePos, setSyringePos] = useState({ x: 0, y: 0 });
  const [syringeRot, setSyringeRot] = useState(0);
  const [hasDrug, setHasDrug] = useState(false);
  const [isShaking, setIsShaking] = useState(false);

  useEffect(() => {
    const interval = setInterval(() => setTimer(t => t + 1), 1000);
    return () => clearInterval(interval);
  }, []);

  const triggerFeedback = (type, message) => {
    setFeedback({ type, message });
    if (navigator.vibrate) navigator.vibrate(type === 'error' ? 200 : 50);
    setTimeout(() => setFeedback(null), 2000);
  };

  const handleShake = () => {
    if (step === 1 && !hasDrug) {
       // Simulating shaking the vial to mix
       setIsShaking(true);
       setTimeout(() => setIsShaking(false), 500);
    } else if (step >= 1) {
      // Shaking with drug = contamination risk
      setContaminated(true);
      triggerFeedback('error', 'CONTAMINATION! Do not shake open systems.');
    }
  };

  const handleDropSyringe = (target) => {
    if (contaminated) return;

    if (step === 0 && target === 'vial') {
      setStep(1);
      triggerFeedback('success', 'Correct. Prepare to withdraw.');
    } else if (step === 1 && target === 'vial' && !hasDrug) {
      // Withdraw logic
      setHasDrug(true);
      triggerFeedback('success', 'Drug withdrawn successfully.');
    } else if (step === 2 && target === 'ivbag' && hasDrug) {
      setStep(3);
      triggerFeedback('success', 'Injection complete!');
      setTimeout(() => onComplete(95), 1500);
    } else if (target === 'floor' || target === 'outside') {
      setContaminated(true);
      triggerFeedback('error', 'Sterility breached! Syringe touched non-sterile area.');
    }
  };

  const stepsInstruction = [
    "Drag Syringe to Vial",
    "Tap Vial to Withdraw Drug",
    "Drag Syringe to IV Bag",
    "Mixing Complete"
  ];

  return (
    <div className="h-full flex flex-col relative">
      {/* Top Bar */}
      <div className="flex justify-between items-center mb-2">
        <Button variant="ghost" size="sm" onClick={onBack}><ChevronLeft /> Exit</Button>
        <div className="flex items-center gap-2 bg-red-50 dark:bg-red-900/30 px-3 py-1 rounded-full text-red-600 dark:text-red-400 font-mono font-bold">
          <Timer size={16} />
          {Math.floor(timer / 60)}:{(timer % 60).toString().padStart(2, '0')}
        </div>
      </div>

      {/* Feedback Toast */}
      <AnimatePresence>
        {feedback && (
          <motion.div
            initial={{ opacity: 0, y: -20 }}
            animate={{ opacity: 1, y: 0 }}
            exit={{ opacity: 0 }}
            className={`absolute top-16 left-0 right-0 mx-auto w-max z-50 px-6 py-3 rounded-xl shadow-xl font-bold text-white ${feedback.type === 'error' ? 'bg-red-500' : 'bg-green-500'}`}
          >
            {feedback.message}
          </motion.div>
        )}
      </AnimatePresence>

      {/* Contamination Warning */}
      {contaminated && (
        <div className="absolute inset-0 z-40 bg-red-500/20 backdrop-blur-sm flex items-center justify-center pointer-events-none">
          <div className="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-2xl text-center border-4 border-red-500">
            <AlertTriangle size={48} className="text-red-500 mx-auto mb-4" />
            <h3 className="text-2xl font-bold text-red-600 mb-2">CONTAMINATED</h3>
            <p className="text-gray-600 dark:text-gray-300">Sterile field breached. Restart required.</p>
            <Button onClick={() => { setContaminated(false); setStep(0); setHasDrug(false); }} className="mt-4" variant="danger">Restart</Button>
          </div>
        </div>
      )}

      {/* Main Simulation Area */}
      <div className="flex-1 bg-gray-100 dark:bg-slate-800 rounded-3xl relative overflow-hidden border-2 border-gray-200 dark:border-slate-700 shadow-inner">
        
        {/* Background Hood Image */}
        <img src={ASSETS.hood} className="absolute inset-0 w-full h-full object-cover opacity-20 pointer-events-none" alt="hood" />

        {/* Instruction Text */}
        <div className="absolute top-4 left-0 right-0 text-center z-10">
          <span className="bg-black/70 text-white px-4 py-2 rounded-full text-sm font-medium backdrop-blur-md">
            {stepsInstruction[step]}
          </span>
        </div>

        {/* Tools Area */}
        <div className="absolute bottom-0 w-full h-1/3 bg-white/50 dark:bg-slate-900/50 backdrop-blur-sm border-t border-gray-200 dark:border-slate-700 flex justify-around items-center px-4 pb-4">
          
          {/* Syringe - Draggable */}
          <motion.div
            drag
            dragMomentum={false}
            onDragEnd={(e, info) => {
              // Simple hit detection logic based on screen position would go here
              // For this demo, we use buttons near targets or simplified zones
            }}
            className="relative w-32 h-32 flex items-center justify-center cursor-grab active:cursor-grabbing touch-none"
            style={{ x: syringePos.x, y: syringePos.y }}
            animate={{ rotate: syringeRot }}
          >
            <div className="relative w-full h-full">
               {/* SVG Syringe */}
               <svg viewBox="0 0 100 100" className="w-full h-full drop-shadow-xl">
                 <rect x="40" y="20" width="20" height="60" fill="#e2e8f0" stroke="#94a3b8" strokeWidth="2" />
                 <rect x="35" y="75" width="30" height="10" fill="#cbd5e1" />
                 <rect x="38" y="15" width="24" height="10" fill="#94a3b8" />
                 <line x1="50" y1="20" x2="50" y2="80" stroke="#3b82f6" strokeWidth={hasDrug ? 8 : 0} opacity="0.8" />
                 {hasDrug && <circle cx="50" cy="25" r="3" fill="#3b82f6" />}
               </svg>
               {contaminated && <div className="absolute inset-0 bg-red-500/50 rounded-full animate-pulse" />}
            </div>
          </motion.div>

        </div>

        {/* Targets Area (Top 2/3) */}
        <div className="absolute top-1/4 left-0 w-full flex justify-around px-8">
          
          {/* Vial Target */}
          <div 
            className={`relative w-24 h-32 transition-all ${step === 0 || step === 1 ? 'opacity-100 scale-100' : 'opacity-50 scale-90'}`}
            onClick={() => handleDropSyringe('vial')}
          >
            <img src={ASSETS.vial} className="w-full h-full object-contain drop-shadow-lg" alt="vial" />
            {isShaking && <motion.div animate={{ rotate: [0, 10, -10, 0] }} transition={{ repeat: Infinity, duration: 0.2 }} className="absolute inset-0" />}
            {step === 1 && !hasDrug && (
              <motion.div 
                animate={{ scale: [1, 1.2, 1] }} 
                transition={{ repeat: Infinity }}
                className="absolute -top-4 -right-4 bg-blue-500 text-white text-xs px-2 py-1 rounded-full font-bold"
              >
                TAP TO WITHDRAW
              </motion.div>
            )}
          </div>

          {/* IV Bag Target */}
          <div 
            className={`relative w-32 h-40 transition-all ${step === 2 ? 'opacity-100 scale-100' : 'opacity-50 scale-90'}`}
            onClick={() => handleDropSyringe('ivbag')}
          >
            <img src={ASSETS.ivBag} className="w-full h-full object-contain drop-shadow-lg" alt="ivbag" />
             {step === 2 && hasDrug && (
              <motion.div 
                animate={{ scale: [1, 1.2, 1] }} 
                transition={{ repeat: Infinity }}
                className="absolute -top-4 -right-4 bg-green-500 text-white text-xs px-2 py-1 rounded-full font-bold"
              >
                TAP TO INJECT
              </motion.div>
            )}
          </div>

        </div>

        {/* Action Buttons Overlay */}
        <div className="absolute bottom-32 right-4 flex flex-col gap-2">
           <button 
             onClick={handleShake}
             className="w-12 h-12 bg-white dark:bg-slate-700 rounded-full shadow-lg flex items-center justify-center text-gray-700 dark:text-gray-200 active:scale-90"
             title="Shake Vial"
           >
             <RotateCcw size={20} />
           </button>
           <button 
             onClick={() => setShowGuidance(!showGuidance)}
             className={`w-12 h-12 rounded-full shadow-lg flex items-center justify-center active:scale-90 ${showGuidance ? 'bg-blue-500 text-white' : 'bg-white dark:bg-slate-700 text-gray-700 dark:text-gray-200'}`}
             title="Toggle Guidance"
           >
             <Info size={20} />
           </button>
        </div>

      </div>

      {/* Helper Text */}
      <div className="mt-4 text-center text-sm text-gray-500">
        {showGuidance ? "Drag items or tap targets to interact. Keep syringe sterile." : "Simulation Mode: Active"}
      </div>
    </div>
  );
}

function AssessmentModule({ onComplete, onBack }) {
  const [currentQ, setCurrentQ] = useState(0);
  const [score, setScore] = useState(0);
  const [selected, setSelected] = useState(null);

  const handleAnswer = (index) => {
    setSelected(index);
    const isCorrect = index === QUIZ_QUESTIONS[currentQ].correct;
    if (isCorrect) setScore(s => s + 1);
    
    setTimeout(() => {
      if (currentQ < QUIZ_QUESTIONS.length - 1) {
        setCurrentQ(c => c + 1);
        setSelected(null);
      } else {
        onComplete(isCorrect ? score + 1 : score);
      }
    }, 1000);
  };

  return (
    <div className="h-full flex flex-col max-w-md mx-auto">
      <div className="flex justify-between items-center mb-6">
        <Button variant="ghost" size="sm" onClick={onBack}><ChevronLeft /> Back</Button>
        <span className="font-bold">Question {currentQ + 1}/{QUIZ_QUESTIONS.length}</span>
      </div>

      <div className="flex-1 flex flex-col justify-center">
        <motion.div
          key={currentQ}
          initial={{ opacity: 0, x: 20 }}
          animate={{ opacity: 1, x: 0 }}
          className="bg-white dark:bg-slate-800 p-8 rounded-3xl shadow-xl border border-gray-100 dark:border-slate-700"
        >
          <h3 className="text-xl font-bold mb-8 text-slate-900 dark:text-white">
            {QUIZ_QUESTIONS[currentQ].question}
          </h3>

          <div className="space-y-4">
            {QUIZ_QUESTIONS[currentQ].options.map((opt, idx) => (
              <button
                key={idx}
                onClick={() => handleAnswer(idx)}
                disabled={selected !== null}
                className={`w-full p-4 rounded-xl text-left font-medium transition-all border-2 
                  ${selected === idx 
                    ? (idx === QUIZ_QUESTIONS[currentQ].correct ? 'bg-green-100 border-green-500 text-green-800' : 'bg-red-100 border-red-500 text-red-800')
                    : 'bg-gray-50 dark:bg-slate-700 border-transparent hover:bg-gray-100 dark:hover:bg-slate-600 text-slate-700 dark:text-slate-200'
                  }
                `}
              >
                {opt}
              </button>
            ))}
          </div>
        </motion.div>
      </div>
    </div>
  );
}

function ResultsScreen({ score, onHome }) {
  return (
    <motion.div 
      initial={{ scale: 0.8, opacity: 0 }}
      animate={{ scale: 1, opacity: 1 }}
      className="h-full flex flex-col items-center justify-center text-center space-y-8"
    >
      <div className="relative">
        <div className="absolute inset-0 bg-green-400 blur-3xl opacity-20 rounded-full"></div>
        <Award size={120} className="text-yellow-500 relative z-10" strokeWidth={1.5} />
      </div>

      <div>
        <h2 className="text-4xl font-bold text-slate-900 dark:text-white mb-2">Certification Earned!</h2>
        <p className="text-slate-500 dark:text-slate-400 text-lg">You have successfully completed the IV Mixing Module.</p>
      </div>

      <div className="grid grid-cols-2 gap-4 w-full max-w-xs">
        <div className="bg-white dark:bg-slate-800 p-4 rounded-2xl shadow-sm border border-gray-100 dark:border-slate-700">
          <div className="text-sm text-gray-500">Score</div>
          <div className="text-2xl font-bold text-blue-600">{score}%</div>
        </div>
        <div className="bg-white dark:bg-slate-800 p-4 rounded-2xl shadow-sm border border-gray-100 dark:border-slate-700">
          <div className="text-sm text-gray-500">Status</div>
          <div className="text-2xl font-bold text-green-600">Passed</div>
        </div>
      </div>

      <div className="w-full max-w-xs pt-8">
        <Button onClick={onHome} size="lg" className="w-full">
          Return to Dashboard
        </Button>
      </div>
    </motion.div>
  );
}
# Iv-dosing-1
