import React, { useState, useEffect, useMemo } from 'react';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer, LineChart, Line, AreaChart, Area } from 'recharts';
import { Zap, Battery, Thermometer, Gauge, MapPin, Wrench, Power, AlertTriangle, Play, Square, Settings2 } from 'lucide-react';

// --- MOCK DATA GENERATION ---
// In a real application, this data would come from a live API feed.
const EXCAVATOR_MODELS = ['EX-250', 'EX-350X', 'EX-470LC', 'EX-550-PRO'];
const STATUSES = ['Operational', 'Idle', 'Warning', 'Maintenance Required'];
const LOCATIONS = ['Quarry A', 'Site B - Sector 7', 'Highway Project Z', 'Downtown Metro Line', 'Port Facility C'];

const generateInitialData = (count) => {
    return Array.from({ length: count }, (_, i) => ({
        id: `EXC-00${i + 1}`,
        model: EXCAVATOR_MODELS[i % EXCAVATOR_MODELS.length],
        status: i % 5 === 0 ? 'Warning' : (i % 7 === 0 ? 'Maintenance Required' : 'Operational'),
        location: LOCATIONS[i % LOCATIONS.length],
        operatingHours: Math.floor(Math.random() * 5000) + 1000,
        engineOn: Math.random() > 0.5,
        metrics: {
            engineRPM: Math.floor(Math.random() * 800) + 1200,
            hydraulicPressure: Math.floor(Math.random() * 500) + 3000,
            engineTemp: Math.floor(Math.random() * 20) + 85,
            fuelLevel: Math.floor(Math.random() * 60) + 20,
            batteryVoltage: parseFloat((Math.random() * 2 + 23).toFixed(2)),
        },
        history: Array.from({ length: 10 }, (_, j) => ({
            name: `T-${10 - j}`,
            rpm: Math.floor(Math.random() * 800) + 1200,
            pressure: Math.floor(Math.random() * 500) + 3000,
        }))
    }));
};

// --- UI COMPONENTS ---

const Header = ({ stats }) => (
    <header className="bg-gray-900/80 backdrop-blur-sm text-white p-4 border-b border-gray-700/50 sticky top-0 z-50">
        <div className="container mx-auto flex justify-between items-center">
            <div className="flex items-center space-x-3">
                <Zap className="h-8 w-8 text-yellow-400" />
                <div>
                    <h1 className="text-2xl font-bold tracking-tight">Excavator Command Center</h1>
                    <p className="text-sm text-gray-400">Real-time Fleet Monitoring & Control</p>
                </div>
            </div>
            <div className="hidden md:flex items-center space-x-6">
                <div className="text-center">
                    <p className="text-xl font-bold">{stats.total}</p>
                    <p className="text-xs text-gray-400">Total Units</p>
                </div>
                <div className="text-center">
                    <p className="text-xl font-bold text-green-400">{stats.operational}</p>
                    <p className="text-xs text-gray-400">Operational</p>
                </div>
                <div className="text-center">
                    <p className="text-xl font-bold text-yellow-400">{stats.warning}</p>
                    <p className="text-xs text-gray-400">Warnings</p>
                </div>
                 <div className="text-center">
                    <p className="text-xl font-bold text-red-500">{stats.maintenance}</p>
                    <p className="text-xs text-gray-400">Maintenance</p>
                </div>
            </div>
        </div>
    </header>
);

const StatCard = ({ icon, label, value, unit, color }) => (
    <div className="bg-gray-800/50 p-4 rounded-lg flex items-center space-x-4 border border-gray-700/50">
        <div className={`p-3 rounded-md bg-${color}-500/10`}>
             {icon}
        </div>
        <div>
            <p className="text-sm text-gray-400">{label}</p>
            <p className="text-2xl font-bold text-white">{value} <span className="text-base font-normal text-gray-400">{unit}</span></p>
        </div>
    </div>
);

const StatusBadge = ({ status }) => {
    const statusStyles = {
        Operational: 'bg-green-500/20 text-green-400 border-green-500/30',
        Idle: 'bg-blue-500/20 text-blue-400 border-blue-500/30',
        Warning: 'bg-yellow-500/20 text-yellow-400 border-yellow-500/30 animate-pulse',
        'Maintenance Required': 'bg-red-500/20 text-red-400 border-red-500/30',
    };
    return (
        <span className={`px-2 py-1 text-xs font-semibold rounded-full border ${statusStyles[status]}`}>
            {status}
        </span>
    );
};

const ExcavatorListItem = ({ excavator, onSelect, isSelected }) => (
    <div
        onClick={() => onSelect(excavator)}
        className={`p-4 rounded-lg cursor-pointer transition-all duration-300 ${isSelected ? 'bg-blue-500/20 ring-2 ring-blue-500' : 'bg-gray-800/60 hover:bg-gray-700/80'}`}
    >
        <div className="flex justify-between items-center">
            <div>
                <p className="font-bold text-white">{excavator.id}</p>
                <p className="text-sm text-gray-400">{excavator.model}</p>
            </div>
            <StatusBadge status={excavator.status} />
        </div>
    </div>
);


const ControlButton = ({ icon, label, onClick, disabled = false, variant = 'primary' }) => {
    const variants = {
        primary: 'bg-blue-600 hover:bg-blue-500 text-white',
        success: 'bg-green-600 hover:bg-green-500 text-white',
        danger: 'bg-red-600 hover:bg-red-500 text-white',
        secondary: 'bg-gray-600 hover:bg-gray-500 text-white',
    }
    return (
        <button
            onClick={onClick}
            disabled={disabled}
            className={`flex flex-col items-center justify-center p-3 rounded-lg transition-all duration-200 space-y-1 w-full h-full disabled:opacity-50 disabled:cursor-not-allowed ${variants[variant]}`}
        >
            {icon}
            <span className="text-xs font-semibold">{label}</span>
        </button>
    );
}

const NotificationPanel = ({ message }) => {
    if (!message) return null;
    return (
        <div className="fixed bottom-4 right-4 bg-gray-900 border border-yellow-500 text-yellow-300 px-4 py-2 rounded-lg shadow-lg flex items-center space-x-2 animate-fade-in-up z-50">
           <AlertTriangle className="h-5 w-5"/>
           <span>{message}</span>
        </div>
    );
}


// --- MAIN APP COMPONENT ---

export default function App() {
    const [excavators, setExcavators] = useState(generateInitialData(12));
    const [selectedExcavator, setSelectedExcavator] = useState(excavators[0]);
    const [notification, setNotification] = useState('');

    // Simulate real-time data updates
    useEffect(() => {
        const interval = setInterval(() => {
            setExcavators(prevExcavators =>
                prevExcavators.map(ex => {
                    const engineOn = ex.engineOn;
                    const newRPM = engineOn ? Math.max(800, ex.metrics.engineRPM + Math.floor(Math.random() * 100) - 50) : 0;
                    const newPressure = engineOn ? Math.max(1000, ex.metrics.hydraulicPressure + Math.floor(Math.random() * 200) - 100) : 0;
                    const newTemp = engineOn ? Math.min(110, ex.metrics.engineTemp + Math.random() * 2 - 1) : Math.max(20, ex.metrics.engineTemp - 1);
                    const newFuel = engineOn ? Math.max(0, ex.metrics.fuelLevel - 0.1) : ex.metrics.fuelLevel;
                    
                    const newHistory = [...ex.history.slice(1), { name: 'Now', rpm: newRPM, pressure: newPressure }];

                    return {
                        ...ex,
                        metrics: {
                            ...ex.metrics,
                            engineRPM: newRPM,
                            hydraulicPressure: newPressure,
                            engineTemp: newTemp,
                            fuelLevel: newFuel,
                            batteryVoltage: parseFloat((23.5 + Math.random() * 2).toFixed(2)),
                        },
                        history: newHistory
                    };
                })
            );
        }, 2000); // Update every 2 seconds

        return () => clearInterval(interval);
    }, []);
    
    // Update selected excavator details when main list updates
    useEffect(() => {
        if(selectedExcavator) {
            const updatedSelected = excavators.find(ex => ex.id === selectedExcavator.id);
            if(updatedSelected) {
                setSelectedExcavator(updatedSelected);
            }
        }
    }, [excavators, selectedExcavator?.id]);

    const showNotification = (message) => {
        setNotification(message);
        setTimeout(() => setNotification(''), 4000);
    }

    const handleControlAction = (action) => {
        if (!selectedExcavator) return;

        showNotification(`Command "${action}" sent to ${selectedExcavator.id}.`);
        
        // Simulate effect of action
        setExcavators(prev => prev.map(ex => {
            if (ex.id === selectedExcavator.id) {
                let newStatus = ex.status;
                let newEngineOn = ex.engineOn;
                
                if (action === 'Start Engine') newEngineOn = true;
                if (action === 'Stop Engine') newEngineOn = false;
                if (action === 'Run Diagnostics') newStatus = 'Idle';
                
                return {...ex, engineOn: newEngineOn, status: newStatus};
            }
            return ex;
        }));
    };

    const fleetStats = useMemo(() => {
        return {
            total: excavators.length,
            operational: excavators.filter(e => e.status === 'Operational' || e.status === 'Idle').length,
            warning: excavators.filter(e => e.status === 'Warning').length,
            maintenance: excavators.filter(e => e.status === 'Maintenance Required').length,
        };
    }, [excavators]);


    return (
        <div className="bg-gray-900 text-gray-200 min-h-screen font-sans">
            <style>{`
                .recharts-tooltip-cursor { fill: rgba(255, 255, 255, 0.1); }
                .recharts-tooltip-wrapper { background-color: #1f2937 !important; border-color: #4b5563 !important; border-radius: 0.5rem !important; }
                .recharts-default-tooltip .recharts-tooltip-label { color: #f3f4f6 !important; }
                @keyframes fade-in-up {
                    from { opacity: 0; transform: translateY(20px); }
                    to { opacity: 1; transform: translateY(0); }
                }
                .animate-fade-in-up { animation: fade-in-up 0.5s ease-out forwards; }
            `}</style>
            
            <Header stats={fleetStats} />
            <NotificationPanel message={notification} />

            <main className="container mx-auto p-4 flex flex-col lg:flex-row gap-4">
                {/* Left Panel: Excavator List */}
                <div className="lg:w-1/4 xl:w-1/5 flex-shrink-0">
                    <div className="bg-gray-800/30 border border-gray-700/50 rounded-lg p-4">
                        <h2 className="text-lg font-bold mb-4 text-white">Fleet Units</h2>
                        <div className="space-y-2 h-[calc(100vh-180px)] overflow-y-auto pr-2">
                            {excavators.map(ex => (
                                <ExcavatorListItem
                                    key={ex.id}
                                    excavator={ex}
                                    onSelect={setSelectedExcavator}
                                    isSelected={selectedExcavator?.id === ex.id}
                                />
                            ))}
                        </div>
                    </div>
                </div>

                {/* Right Panel: Details & Controls */}
                <div className="lg:w-3/4 xl:w-4/5">
                    {selectedExcavator ? (
                        <div className="flex flex-col gap-4">
                            {/* Top Detail Row */}
                            <div className="bg-gray-800/30 border border-gray-700/50 rounded-lg p-6">
                                <div className="flex flex-col md:flex-row justify-between items-start md:items-center">
                                    <div>
                                        <h2 className="text-3xl font-bold text-white">{selectedExcavator.id}</h2>
                                        <p className="text-gray-400">{selectedExcavator.model}</p>
                                    </div>
                                    <div className="mt-4 md:mt-0 flex flex-col md:flex-row md:items-center gap-4 md:gap-8">
                                       <div className="flex items-center gap-2 text-gray-300">
                                            <MapPin size={16} />
                                            <span>{selectedExcavator.location}</span>
                                        </div>
                                       <div className="flex items-center gap-2 text-gray-300">
                                            <Gauge size={16} />
                                            <span>{selectedExcavator.operatingHours.toLocaleString()} hours</span>
                                        </div>
                                        <div className="flex items-center gap-2">
                                            <StatusBadge status={selectedExcavator.status} />
                                        </div>
                                    </div>
                                </div>
                            </div>
                            
                            {/* Metrics Grid */}
                            <div className="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-4 gap-4">
                                <StatCard icon={<Zap size={24} className="text-yellow-400"/>} label="Engine Speed" value={selectedExcavator.metrics.engineRPM} unit="RPM" color="yellow" />
                                <StatCard icon={<Settings2 size={24} className="text-blue-400"/>} label="Hydraulic Pressure" value={selectedExcavator.metrics.hydraulicPressure} unit="psi" color="blue" />
                                <StatCard icon={<Thermometer size={24} className="text-red-400"/>} label="Engine Temp" value={selectedExcavator.metrics.engineTemp.toFixed(1)} unit="°C" color="red" />
                                <StatCard icon={<Battery size={24} className="text-green-400"/>} label="Fuel Level" value={selectedExcavator.metrics.fuelLevel.toFixed(0)} unit="%" color="green" />
                            </div>

                            {/* Charts & Controls */}
                            <div className="grid grid-cols-1 lg:grid-cols-3 gap-4">
                                <div className="lg:col-span-2 bg-gray-800/30 border border-gray-700/50 rounded-lg p-4">
                                    <h3 className="font-bold text-white mb-4">Performance History (RPM vs Pressure)</h3>
                                    <div style={{ width: '100%', height: 250 }}>
                                        <ResponsiveContainer>
                                            <AreaChart data={selectedExcavator.history} margin={{ top: 5, right: 20, left: -10, bottom: 5 }}>
                                                <defs>
                                                    <linearGradient id="colorRpm" x1="0" y1="0" x2="0" y2="1">
                                                        <stop offset="5%" stopColor="#facc15" stopOpacity={0.8}/>
                                                        <stop offset="95%" stopColor="#facc15" stopOpacity={0}/>
                                                    </linearGradient>
                                                    <linearGradient id="colorPressure" x1="0" y1="0" x2="0" y2="1">
                                                        <stop offset="5%" stopColor="#60a5fa" stopOpacity={0.8}/>
                                                        <stop offset="95%" stopColor="#60a5fa" stopOpacity={0}/>
                                                    </linearGradient>
                                                </defs>
                                                <CartesianGrid strokeDasharray="3 3" stroke="#4b5563" />
                                                <XAxis dataKey="name" stroke="#9ca3af" fontSize={12} />
                                                <YAxis stroke="#9ca3af" fontSize={12} />
                                                <Tooltip contentStyle={{ backgroundColor: '#1f2937', border: '1px solid #4b5563' }} />
                                                <Legend />
                                                <Area type="monotone" dataKey="rpm" stroke="#facc15" fillOpacity={1} fill="url(#colorRpm)" name="Engine RPM" />
                                                <Area type="monotone" dataKey="pressure" stroke="#60a5fa" fillOpacity={1} fill="url(#colorPressure)" name="Hydraulic Pressure" />
                                            </AreaChart>
                                        </ResponsiveContainer>
                                    </div>
                                </div>
                                <div className="bg-gray-800/30 border border-gray-700/50 rounded-lg p-4">
                                    <h3 className="font-bold text-white mb-4">Remote Operations</h3>
                                    <div className="grid grid-cols-2 gap-3 h-[calc(100%-40px)]">
                                        <ControlButton 
                                            icon={<Play size={24} />} 
                                            label="Start Engine"
                                            onClick={() => handleControlAction('Start Engine')}
                                            disabled={selectedExcavator.engineOn}
                                            variant="success"
                                        />
                                        <ControlButton 
                                            icon={<Square size={24} />} 
                                            label="Stop Engine"
                                            onClick={() => handleControlAction('Stop Engine')}
                                            disabled={!selectedExcavator.engineOn}
                                            variant="danger"
                                        />
                                        <ControlButton 
                                            icon={<Wrench size={24} />} 
                                            label="Run Diagnostics"
                                            onClick={() => handleControlAction('Run Diagnostics')}
                                            variant="secondary"
                                            className="col-span-2"
                                        />
                                    </div>
                                </div>
                            </div>
                        </div>
                    ) : (
                        <div className="flex items-center justify-center h-full bg-gray-800/30 rounded-lg">
                            <p className="text-gray-500">Select an excavator to view details</p>
                        </div>
                    )}
                </div>
            </main>
        </div>
    );
}

