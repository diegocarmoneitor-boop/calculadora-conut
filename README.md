import React, { useState, useMemo } from 'react';
import { drugDatabase } from './data';
import { Drug, DrugFamily, RiskLevel } from './types';
import { Timeline } from './components/Timeline';
import { 
  Activity, 
  AlertTriangle, 
  ChevronDown, 
  FileText, 
  HeartPulse, 
  Info, 
  Search, 
  ShieldAlert, 
  Stethoscope 
} from 'lucide-react';

const App: React.FC = () => {
  const [selectedFamilyId, setSelectedFamilyId] = useState<string>('');
  const [selectedDrugId, setSelectedDrugId] = useState<string>('');
  const [surgeryRisk, setSurgeryRisk] = useState<RiskLevel>('moderate');

  // Derived state
  const selectedFamily = useMemo(
    () => drugDatabase.find((f) => f.id === selectedFamilyId),
    [selectedFamilyId]
  );

  const selectedDrug = useMemo(
    () => selectedFamily?.drugs.find((d) => d.id === selectedDrugId),
    [selectedFamily, selectedDrugId]
  );

  // Handlers
  const handleFamilyChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
    setSelectedFamilyId(e.target.value);
    setSelectedDrugId(''); // Reset drug when family changes
  };

  const handleDrugChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
    setSelectedDrugId(e.target.value);
  };

  return (
    <div className="min-h-screen bg-slate-50 font-sans pb-20">
      
      {/* 1. HEADER GLOBAL */}
      <header className="sticky top-0 z-50 bg-white/80 backdrop-blur-md border-b border-slate-200 shadow-sm">
        <div className="max-w-[1400px] w-[95%] mx-auto px-4 py-3 flex justify-between items-center">
          <div className="flex items-center gap-3">
            <div className="bg-medical-600 text-white p-2 rounded-lg">
              <Stethoscope className="w-6 h-6" />
            </div>
            <div>
              <h1 className="text-lg md:text-xl font-bold text-slate-800 leading-tight">
                Conciliación Perioperatoria
              </h1>
              <p className="text-xs text-slate-500 font-medium hidden md:block">
                Herramienta de apoyo clínico farmacéutico
              </p>
            </div>
          </div>
          <div className="hidden md:flex items-center gap-2 bg-amber-50 border border-amber-200 text-amber-800 px-3 py-1.5 rounded-full text-xs font-semibold">
            <AlertTriangle className="w-3 h-3" />
            <span>Ayuda a la decisión. No sustituye juicio clínico.</span>
          </div>
        </div>
      </header>

      <main className="max-w-[1400px] w-[95%] mx-auto pt-8">

        {/* 2. PANEL (Dashboard) - Only visible if no drug selected or just starting */}
        {!selectedDrug && (
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-12 animate-fade-in">
            <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
              <div className="w-10 h-10 bg-blue-50 rounded-full flex items-center justify-center mb-4">
                <Info className="w-5 h-5 text-blue-600" />
              </div>
              <h3 className="font-bold text-slate-800 mb-2">¿Qué es esto?</h3>
              <p className="text-sm text-slate-600">
                Guía rápida para ajustar medicación crónica antes de una cirugía programada, minimizando riesgos y complicaciones.
              </p>
            </div>
            <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
              <div className="w-10 h-10 bg-red-50 rounded-full flex items-center justify-center mb-4">
                <HeartPulse className="w-5 h-5 text-red-600" />
              </div>
              <h3 className="font-bold text-slate-800 mb-2">Riesgos Evaluados</h3>
              <p className="text-sm text-slate-600">
                Hemorragia intraoperatoria, eventos trombóticos (stent/ictus), inestabilidad hemodinámica y descompensación metabólica.
              </p>
            </div>
            <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
              <div className="w-10 h-10 bg-purple-50 rounded-full flex items-center justify-center mb-4">
                <ShieldAlert className="w-5 h-5 text-purple-600" />
              </div>
              <h3 className="font-bold text-slate-800 mb-2">Seguridad ante todo</h3>
              <p className="text-sm text-slate-600">
                Basado en guías ESA, ACC/AHA y fichas técnicas. Prioriza siempre la vida del paciente sobre el protocolo.
              </p>
            </div>
            <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
              <div className="w-10 h-10 bg-green-50 rounded-full flex items-center justify-center mb-4">
                <Activity className="w-5 h-5 text-green-600" />
              </div>
              <h3 className="font-bold text-slate-800 mb-2">Para Especialistas</h3>
              <p className="text-sm text-slate-600">
                Diseñado para Cirujanos, Anestesistas, Farmacéuticos Hospitalarios e Internistas en preanestesia.
              </p>
            </div>
          </div>
        )}

        {/* MAIN INTERACTION AREA */}
        <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
          
          {/* 3. SECTION: SELECTION (Left Column) */}
          <div className="lg:col-span-4 space-y-6">
            <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200 sticky top-24">
              <h2 className="text-lg font-bold text-slate-800 mb-6 flex items-center gap-2">
                <Search className="w-5 h-5 text-slate-400" />
                Selector de Fármaco
              </h2>

              {/* Family Selector */}
              <div className="mb-6">
                <label className="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">
                  1. Familia Farmacológica
                </label>
                <div className="relative">
                  <select
                    value={selectedFamilyId}
                    onChange={handleFamilyChange}
                    className="w-full appearance-none bg-slate-50 border border-slate-300 text-slate-800 py-3 px-4 rounded-lg focus:outline-none focus:ring-2 focus:ring-medical-500 focus:border-transparent transition-all"
                  >
                    <option value="">Seleccionar familia...</option>
                    {drugDatabase.map((family) => (
                      <option key={family.id} value={family.id}>
                        {family.name}
                      </option>
                    ))}
                  </select>
                  <ChevronDown className="absolute right-4 top-3.5 w-5 h-5 text-slate-400 pointer-events-none" />
                </div>
              </div>

              {/* Drug Selector */}
              <div className="mb-6">
                <label className={`block text-xs font-semibold uppercase tracking-wider mb-2 transition-colors ${selectedFamilyId ? 'text-slate-500' : 'text-slate-300'}`}>
                  2. Principio Activo
                </label>
                <div className="relative">
                  <select
                    value={selectedDrugId}
                    onChange={handleDrugChange}
                    disabled={!selectedFamilyId}
                    className={`w-full appearance-none border py-3 px-4 rounded-lg focus:outline-none focus:ring-2 focus:ring-medical-500 transition-all ${
                      selectedFamilyId 
                        ? 'bg-slate-50 border-slate-300 text-slate-800' 
                        : 'bg-slate-100 border-slate-200 text-slate-400 cursor-not-allowed'
                    }`}
                  >
                    <option value="">
                      {selectedFamilyId ? 'Seleccionar fármaco...' : '---'}
                    </option>
                    {selectedFamily?.drugs.map((drug) => (
                      <option key={drug.id} value={drug.id}>
                        {drug.name}
                      </option>
                    ))}
                  </select>
                  <ChevronDown className="absolute right-4 top-3.5 w-5 h-5 text-slate-400 pointer-events-none" />
                </div>
              </div>

              {/* Surgery Risk Context (Optional but useful logic placeholder) */}
              <div className="pt-6 border-t border-slate-100">
                <label className="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-3">
                  Contexto Quirúrgico
                </label>
                <div className="grid grid-cols-3 gap-2">
                  {(['low', 'moderate', 'high'] as const).map((level) => (
                    <button
                      key={level}
                      onClick={() => setSurgeryRisk(level)}
                      className={`py-2 px-2 rounded-lg text-xs font-medium border transition-all ${
                        surgeryRisk === level
                          ? 'bg-medical-50 border-medical-500 text-medical-900 shadow-sm'
                          : 'bg-white border-slate-200 text-slate-600 hover:bg-slate-50'
                      }`}
                    >
                      {level === 'low' ? 'Bajo' : level === 'moderate' ? 'Moderado' : 'Alto'} Riesgo
                    </button>
                  ))}
                </div>
                <p className="text-[10px] text-slate-400 mt-2">
                  *Afecta a recomendaciones de antiagregantes y anticoagulantes.
                </p>
              </div>
            </div>
          </div>

          {/* 4. SECTION: RECOMMENDATION & DETAILS (Right Column) */}
          <div className="lg:col-span-8">
            {selectedDrug ? (
              <div className="space-y-6 animate-slide-up">
                
                {/* Main Card */}
                <div className="bg-white rounded-xl shadow-lg border-t-4 border-medical-500 overflow-hidden">
                  <div className="p-6 md:p-8">
                    {/* Header of Card */}
                    <div className="flex flex-col md:flex-row md:justify-between md:items-start gap-4 mb-6">
                      <div>
                        <span className="inline-block px-3 py-1 bg-slate-100 text-slate-600 rounded-full text-xs font-semibold uppercase tracking-wide mb-2">
                          {selectedFamily?.name}
                        </span>
                        <h2 className="text-2xl md:text-3xl font-bold text-slate-900">
                          {selectedDrug.name}
                        </h2>
                        <p className="text-slate-500 font-medium">
                          {selectedDrug.genericName}
                        </p>
                      </div>
                      
                      {/* Risk Context Badge in Result */}
                      {surgeryRisk === 'high' && (
                         <div className="flex items-center gap-2 px-3 py-2 bg-red-50 text-red-700 rounded-lg text-sm font-medium border border-red-100">
                           <AlertTriangle className="w-4 h-4" />
                           Cirugía Alto Riesgo
                         </div>
                      )}
                    </div>

                    {/* Primary Recommendation */}
                    <div className="bg-medical-50 p-5 rounded-lg border-l-4 border-medical-600 mb-8">
                      <h4 className="text-medical-900 font-bold text-sm uppercase mb-1 opacity-80">
                        Recomendación Farmacéutica
                      </h4>
                      <p className="text-lg md:text-xl font-medium text-slate-800">
                        {selectedDrug.recommendationSummary}
                      </p>
                    </div>

                    {/* Visual Timeline */}
                    <Timeline guide={selectedDrug.guide} />
                  </div>
                </div>

                {/* 5. SECTION: DETAILS & MATICES */}
                <div className="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                  
                  {/* Clinical Reasoning */}
                  <details className="group border-b border-slate-100" open>
                    <summary className="flex items-center justify-between p-6 cursor-pointer bg-slate-50 hover:bg-slate-100 transition-colors">
                      <div className="flex items-center gap-3">
                        <FileText className="w-5 h-5 text-slate-500" />
                        <h3 className="font-bold text-slate-700">Justificación Clínica</h3>
                      </div>
                      <ChevronDown className="w-5 h-5 text-slate-400 group-open:rotate-180 transition-transform" />
                    </summary>
                    <div className="p-6 text-slate-600 leading-relaxed">
                      {selectedDrug.clinicalReasoning}
                    </div>
                  </details>

                  {/* Warnings */}
                  <details className="group border-b border-slate-100">
                    <summary className="flex items-center justify-between p-6 cursor-pointer bg-slate-50 hover:bg-slate-100 transition-colors">
                      <div className="flex items-center gap-3">
                        <AlertTriangle className="w-5 h-5 text-amber-500" />
                        <h3 className="font-bold text-slate-700">Advertencias Clave</h3>
                      </div>
                      <ChevronDown className="w-5 h-5 text-slate-400 group-open:rotate-180 transition-transform" />
                    </summary>
                    <div className="p-6">
                      <ul className="space-y-3">
                        {selectedDrug.warnings.map((warning, idx) => (
                          <li key={idx} className="flex items-start gap-3">
                            <span className="mt-1.5 w-1.5 h-1.5 bg-amber-500 rounded-full flex-shrink-0" />
                            <span className="text-slate-600">{warning}</span>
                          </li>
                        ))}
                      </ul>
                    </div>
                  </details>

                  {/* Special Considerations */}
                  {selectedDrug.specialConsiderations && (
                    <details className="group">
                      <summary className="flex items-center justify-between p-6 cursor-pointer bg-slate-50 hover:bg-slate-100 transition-colors">
                        <div className="flex items-center gap-3">
                          <Stethoscope className="w-5 h-5 text-purple-500" />
                          <h3 className="font-bold text-slate-700">Situaciones Especiales</h3>
                        </div>
                        <ChevronDown className="w-5 h-5 text-slate-400 group-open:rotate-180 transition-transform" />
                      </summary>
                      <div className="p-6 text-slate-600 leading-relaxed bg-purple-50/30">
                        {selectedDrug.specialConsiderations}
                      </div>
                    </details>
                  )}
                </div>

              </div>
            ) : (
              // Empty State
              <div className="h-full min-h-[400px] flex flex-col items-center justify-center bg-white rounded-xl border-2 border-dashed border-slate-200 p-8 text-center opacity-70">
                <Search className="w-16 h-16 text-slate-300 mb-4" />
                <h3 className="text-xl font-bold text-slate-400">Esperando selección</h3>
                <p className="text-slate-400 max-w-sm mt-2">
                  Selecciona una familia y un fármaco en el panel izquierdo para ver las recomendaciones de conciliación.
                </p>
              </div>
            )}
          </div>

        </div>
      </main>
    </div>
  );
};

export default App;
