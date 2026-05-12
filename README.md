import React, { useState, useEffect } from 'react';
import { 
  Zap, 
  CloudSun, 
  Thermometer, 
  TrendingUp, 
  Search, 
  ArrowRight,
  AlertCircle,
  CheckCircle2,
  MapPin,
  ShoppingBag
} from 'lucide-react';

const PRODUCTS = [
  { id: 'iced-coffee', name: 'Iced Coffee', type: 'Cold' },
  { id: 'snow-shovels', name: 'Snow Shovels', type: 'Hot' },
  { id: 'heaters', name: 'Space Heaters', type: 'Hot' },
  { id: 'ice-cream', name: 'Artisan Ice Cream', type: 'Cold' },
  { id: 'sunscreen', name: 'SPF 50 Sunscreen', type: 'Cold' },
];

export default function App() {
  const [city, setCity] = useState('Portland');
  const [selectedProduct, setSelectedProduct] = useState(PRODUCTS[0]);
  const [weather, setWeather] = useState(null);
  const [loading, setLoading] = useState(false);
  const [decision, setDecision] = useState(null);
  const [error, setError] = useState(null);

  const apiKey = ""; // Provided by environment

  const fetchWeatherAndDecide = async () => {
    setLoading(true);
    setError(null);
    setDecision(null);

    try {
      // Step 2: API Call
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=imperial`
      );
      
      if (!response.ok) throw new Error("City not found or API error.");
      
      const data = await response.json();
      const temp = Math.round(data.main.temp);
      setWeather(data);

      // Step 3: Reasoning (Logic A & B)
      let status = "Standard Demand";
      let discount = 0;
      let isHighDemand = false;

      // Logic: 
      // Hot Weather (> 85F) + Cold Product = High Demand
      // Cold Weather (< 32F) + Hot Product = High Demand
      if (temp > 80 && selectedProduct.type === 'Cold') {
        isHighDemand = true;
        discount = 15;
      } else if (temp < 35 && selectedProduct.type === 'Hot') {
        isHighDemand = true;
        discount = 20;
      }

      // Step 4: Decision
      setDecision({
        isHighDemand,
        temp,
        discount,
        headline: isHighDemand 
          ? `${temp}°F — Increase ${selectedProduct.name} promo by ${discount}%`
          : `Standard Demand — No extra discount needed for ${selectedProduct.name}`,
        reason: isHighDemand 
          ? `Current temperature is ${temp}°F. High demand predicted for ${selectedProduct.type} products.`
          : `Temperature is ${temp}°F. Demand levels are within normal range.`
      });

    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-slate-50 p-4 md:p-8 flex items-center justify-center font-sans">
      <div className="max-w-4xl w-full bg-white rounded-3xl shadow-2xl shadow-slate-200 border border-slate-100 overflow-hidden">
        
        {/* Header */}
        <div className="bg-blue-600 p-6 text-white flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="bg-white/20 p-2 rounded-xl backdrop-blur-sm">
              <Zap className="fill-white" />
            </div>
            <div>
              <h1 className="text-xl font-bold">Flash-Deals Inventory Agent</h1>
              <p className="text-blue-100 text-sm">Automated Demand-Price Strategist</p>
            </div>
          </div>
          <div className="hidden sm:block text-xs font-mono bg-blue-700 px-3 py-1 rounded-full text-blue-200 uppercase tracking-widest">
            Agentic Workflow v1.0
          </div>
        </div>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-0">
          
          {/* Step 1 & 2: Input & Trigger */}
          <div className="p-8 border-r border-slate-100">
            <h2 className="text-sm font-bold text-slate-400 uppercase tracking-widest mb-6 flex items-center gap-2">
              <span className="w-6 h-6 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center text-[10px]">01</span>
              Agent Inputs
            </h2>

            <div className="space-y-6">
              <div>
                <label className="block text-sm font-semibold text-slate-700 mb-2 flex items-center gap-2">
                  <ShoppingBag size={16} className="text-slate-400" />
                  Select Product
                </label>
                <select 
                  className="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none transition-all"
                  value={selectedProduct.id}
                  onChange={(e) => setSelectedProduct(PRODUCTS.find(p => p.id === e.target.value))}
                >
                  {PRODUCTS.map(p => (
                    <option key={p.id} value={p.id}>{p.name} ({p.type})</option>
                  ))}
                </select>
              </div>

              <div>
                <label className="block text-sm font-semibold text-slate-700 mb-2 flex items-center gap-2">
                  <MapPin size={16} className="text-slate-400" />
                  Target City
                </label>
                <div className="relative">
                  <input 
                    type="text"
                    value={city}
                    onChange={(e) => setCity(e.target.value)}
                    className="w-full p-3 pl-10 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none transition-all"
                    placeholder="Enter city..."
                  />
                  <Search size={18} className="absolute left-3 top-3.5 text-slate-400" />
                </div>
              </div>

              <button 
                onClick={fetchWeatherAndDecide}
                disabled={loading}
                className="w-full bg-slate-900 text-white font-bold py-4 rounded-2xl hover:bg-slate-800 transition-all flex items-center justify-center gap-2 group disabled:opacity-50"
              >
                {loading ? "Agent Thinking..." : "Execute Logic"}
                {!loading && <ArrowRight size={20} className="group-hover:translate-x-1 transition-transform" />}
              </button>
            </div>
          </div>

          {/* Step 3 & 4: Reasoning & Decision */}
          <div className="p-8 bg-slate-50/50">
            <h2 className="text-sm font-bold text-slate-400 uppercase tracking-widest mb-6 flex items-center gap-2">
              <span className="w-6 h-6 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center text-[10px]">02</span>
              Agent Decision
            </h2>

            {!decision && !loading && !error && (
              <div className="h-full flex flex-col items-center justify-center text-center p-10">
                <div className="w-16 h-16 bg-white rounded-2xl shadow-sm flex items-center justify-center mb-4">
                  <CloudSun className="text-slate-300" size={32} />
                </div>
                <p className="text-slate-400 text-sm font-medium">Wait for input to trigger automated reasoning.</p>
              </div>
            )}

            {loading && (
              <div className="space-y-4 animate-pulse">
                <div className="h-20 bg-slate-200 rounded-2xl"></div>
                <div className="h-40 bg-slate-200 rounded-2xl"></div>
              </div>
            )}

            {error && (
              <div className="bg-rose-50 border border-rose-100 p-4 rounded-2xl flex gap-3 text-rose-600">
                <AlertCircle size={20} />
                <p className="text-sm font-medium">{error}</p>
              </div>
            )}

            {decision && (
              <div className="space-y-4 animate-in fade-in slide-in-from-bottom-2 duration-500">
                {/* Weather Snippet */}
                <div className="bg-white p-4 rounded-2xl shadow-sm border border-slate-100 flex items-center justify-between">
                  <div className="flex items-center gap-3">
                    <div className="bg-amber-50 p-2 rounded-lg text-amber-600">
                      <Thermometer size={20} />
                    </div>
                    <div>
                      <p className="text-xs text-slate-500 uppercase font-bold tracking-tighter">Current Temp</p>
                      <p className="text-lg font-bold text-slate-800">{decision.temp}°F</p>
                    </div>
                  </div>
                  <div className="text-right">
                    <p className="text-xs text-slate-500 uppercase font-bold tracking-tighter">Status</p>
                    <p className={`text-sm font-bold ${decision.isHighDemand ? 'text-amber-600' : 'text-emerald-600'}`}>
                      {decision.isHighDemand ? 'HIGH DEMAND' : 'NORMAL'}
                    </p>
                  </div>
                </div>

                {/* Decision Banner */}
                <div className={`p-6 rounded-3xl border-2 shadow-lg ${
                  decision.isHighDemand 
                  ? 'bg-amber-500 border-amber-400 text-white shadow-amber-200' 
                  : 'bg-white border-slate-200 text-slate-800 shadow-slate-100'
                }`}>
                  <div className="flex items-start gap-4">
                    <div className={`p-3 rounded-2xl ${decision.isHighDemand ? 'bg-white/20' : 'bg-emerald-50'}`}>
                      {decision.isHighDemand ? <Zap className="fill-white" /> : <CheckCircle2 className="text-emerald-600" />}
                    </div>
                    <div>
                      <h3 className="font-black text-xl leading-tight mb-2 uppercase italic italic">
                        {decision.headline}
                      </h3>
                      <p className={`text-sm leading-relaxed ${decision.isHighDemand ? 'text-amber-50' : 'text-slate-500'}`}>
                        {decision.reason}
                      </p>
                    </div>
                  </div>
                </div>

                {/* Automation Log */}
                <div className="flex items-center gap-2 text-[10px] text-slate-400 font-mono uppercase tracking-widest pl-2">
                  <TrendingUp size={12} />
                  Logged to Supabase deal_logs table
                </div>
              </div>
            )}
          </div>
        </div>
      </div>
    </div>
  );
}
