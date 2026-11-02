import React, { useState } from 'react';
import {
  Search, Shield, CheckCircle, Clock, AlertTriangle, MessageCircle,
  FileText, Filter, Building, Award, Check, X, Smartphone, Scale
} from 'lucide-react';

const INITIAL_SCHOOLS = [
  {
    id: 1,
    name: 'Autoescuela GO!!!',
    province: 'Madrid',
    city: 'Madrid',
    dgtCode: 'AE-1024',
    cif: 'B87654321',
    licenses: ['B', 'A2', 'A', 'CAP Mercancías'],
    capOffered: true,
    rating: 4.8,
    priceB: 199,
    status: 'approved',
    phone: '34642353664',
    description: 'Especialistas en carnet B intensivo y CAP profesional.'
  }
];

export default function App() {
  const [schools, setSchools] = useState(INITIAL_SCHOOLS);
  const [selectedProvince, setSelectedProvince] = useState('');
  const [selectedLicense, setSelectedLicense] = useState('');
  const [filterCap, setFilterCap] = useState(false);
  const [activeTab, setActiveTab] = useState('search');
  const [activeLegalModal, setActiveLegalModal] = useState(null);

  const [newSchool, setNewSchool] = useState({
    name: '', province: 'Madrid', city: '', cif: '', dgtCode: '',
    licenses: [], capOffered: false, priceB: '', phone: '', email: ''
  });
  const [formSubmitted, setFormSubmitted] = useState(false);

  const handleRegisterSubmit = (e) => {
    e.preventDefault();
    const entry = {
      ...newSchool,
      id: Date.now(),
      status: 'pending',
      rating: 5.0,
      priceB: Number(newSchool.priceB) || 0
    };
    setSchools([...schools, entry]);
    setFormSubmitted(true);
  };

  const approveSchool = (id) => {
    setSchools(schools.map(s => s.id === id ? { ...s, status: 'approved' } : s));
  };

  const rejectSchool = (id) => {
    setSchools(schools.filter(s => s.id !== id));
  };

  const filteredSchools = schools.filter(s => {
    if (s.status !== 'approved') return false;
    if (selectedProvince && s.province !== selectedProvince) return false;
    if (selectedLicense && !s.licenses.includes(selectedLicense)) return false;
    if (filterCap && !s.capOffered) return false;
    return true;
  });

  const pendingSchools = schools.filter(s => s.status === 'pending');

  return (
    <div className="min-h-screen bg-slate-50 text-slate-900 font-sans flex flex-col justify-between">
      <div>
        {/* BANNER FLOTANTE PWA PARA ANDROID */}
        <div className="bg-blue-900 text-white text-xs py-2 px-4 flex justify-between items-center md:hidden">
          <div className="flex items-center gap-2">
            <Smartphone className="w-4 h-4 text-blue-300" />
            <span>¿Usas Android? Añádela a tu pantalla de inicio</span>
          </div>
          <span className="font-bold underline cursor-pointer">Instalar</span>
        </div>

        {/* HEADER */}
        <header className="bg-blue-600 text-white shadow-md sticky top-0 z-40">
          <div className="max-w-7xl mx-auto px-4 py-3 flex justify-between items-center">
            <div className="flex items-center gap-2 cursor-pointer" onClick={() => setActiveTab('search')}>
              <Building className="w-7 h-7" />
              <h1 className="font-bold text-xl tracking-tight">AutoEscuelaCompare España</h1>
            </div>

            <div className="flex items-center gap-4 text-xs font-semibold">
              <span>ESPAÑA (DGT & CAP)</span>
            </div>
          </div>

          <nav className="bg-blue-700 px-4 flex gap-2 overflow-x-auto text-sm font-medium border-t border-blue-500">
            <button onClick={() => setActiveTab('search')} className={`py-2.5 px-4 flex items-center gap-2 border-b-2 ${activeTab === 'search' ? 'border-white text-white font-bold' : 'border-transparent text-blue-100'}`}>
              <Search className="w-4 h-4" /> Buscar Autoescuelas
            </button>
            <button onClick={() => setActiveTab('register')} className={`py-2.5 px-4 flex items-center gap-2 border-b-2 ${activeTab === 'register' ? 'border-white text-white font-bold' : 'border-transparent text-blue-100'}`}>
              <Award className="w-4 h-4" /> Registrar mi Autoescuela
            </button>
            <button onClick={() => setActiveTab('admin')} className={`py-2.5 px-4 flex items-center gap-2 border-b-2 ${activeTab === 'admin' ? 'border-white text-white font-bold' : 'border-transparent text-blue-100'}`}>
              <Shield className="w-4 h-4" /> Panel Verificación Admin
              {pendingSchools.length > 0 && <span className="bg-red-500 text-white text-xs px-2 py-0.5 rounded-full">{pendingSchools.length}</span>}
            </button>
            <button onClick={() => setActiveTab('billing')} className={`py-2.5 px-4 flex items-center gap-2 border-b-2 ${activeTab === 'billing' ? 'border-white text-white font-bold' : 'border-transparent text-blue-100'}`}>
              <FileText className="w-4 h-4" /> Facturación
            </button>
          </nav>
        </header>

        {/* CONTENIDO PRINCIPAL */}
        <main className="max-w-7xl mx-auto px-4 py-8">
          {activeTab === 'search' && (
            <div>
              <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200 mb-8">
                <h2 className="text-lg font-bold mb-4 flex items-center gap-2">
                  <Filter className="w-5 h-5 text-blue-600" /> Filtros de Búsqueda
                </h2>
                <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                  <div>
                    <label className="block text-xs font-semibold text-slate-500 uppercase mb-1">Provincia</label>
                    <select value={selectedProvince} onChange={(e) => setSelectedProvince(e.target.value)} className="w-full border rounded-lg p-2.5 bg-slate-50">
                      <option value="">Todas las provincias</option>
                      <option value="Madrid">Madrid</option>
                      <option value="Barcelona">Barcelona</option>
                      <option value="Valencia">Valencia</option>
                    </select>
                  </div>
                  <div>
                    <label className="block text-xs font-semibold text-slate-500 uppercase mb-1">Tipo de Carnet</label>
                    <select value={selectedLicense} onChange={(e) => setSelectedLicense(e.target.value)} className="w-full border rounded-lg p-2.5 bg-slate-50">
                      <option value="">Todos los carnets</option>
                      <option value="AM">AM</option>
                      <option value="A2">A2</option>
                      <option value="B">B</option>
                      <option value="C">C</option>
                      <option value="D">D</option>
                    </select>
                  </div>
                  <div className="flex items-center gap-2 pt-6">
                    <input type="checkbox" id="cap" checked={filterCap} onChange={(e) => setFilterCap(e.target.checked)} className="w-5 h-5 text-blue-600 rounded" />
                    <label htmlFor="cap" className="text-sm font-medium cursor-pointer">Solo con CAP</label>
                  </div>
                </div>
              </div>

              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                {filteredSchools.map(school => (
                  <div key={school.id} className="bg-white rounded-xl shadow-sm border border-slate-200 p-6 flex flex-col justify-between">
                    <div>
                      <div className="flex justify-between items-start mb-2">
                        <div>
                          <h3 className="text-xl font-bold text-slate-900">{school.name}</h3>
                          <p className="text-sm text-slate-500">{school.city}, {school.province}</p>
                        </div>
                        <span className="bg-emerald-50 text-emerald-700 border border-emerald-200 text-xs font-semibold px-2.5 py-1 rounded-full flex items-center gap-1">
                          <CheckCircle className="w-3.5 h-3.5" /> Verificada DGT
                        </span>
                      </div>
                      <p className="text-sm text-slate-600 my-3">{school.description}</p>
                    </div>
                    <div className="border-t pt-4 mt-4 flex justify-between items-center">
                      <div>
                        <p className="text-xs text-slate-400">Precio base desde</p>
                        <p className="text-2xl font-black text-blue-600">{school.priceB}€</p>
                      </div>
                      <a href={`https://wa.me/34642353664?text=Hola,%20busco%20información%20de%20su%20autoescuela.`} target="_blank" rel="noreferrer" className="bg-emerald-600 hover:bg-emerald-700 text-white font-medium text-sm px-4 py-2.5 rounded-lg flex items-center gap-2">
                        <MessageCircle className="w-4 h-4" /> Contactar por WhatsApp
                      </a>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          )}

          {activeTab === 'register' && (
            <div className="max-w-2xl mx-auto bg-white p-8 rounded-xl shadow-sm border border-slate-200">
              <h2 className="text-2xl font-bold mb-2">Solicitud de Registro de Autoescuela</h2>
              <p className="text-slate-500 text-sm mb-6">Tus datos serán verificados con la DGT antes de la activación pública.</p>
              {formSubmitted ? (
                <div className="bg-blue-50 border border-blue-200 text-blue-800 p-6 rounded-xl text-center">
                  <Clock className="w-12 h-12 text-blue-600 mx-auto mb-3" />
                  <h3 className="font-bold text-lg mb-1">Solicitud en revisión</h3>
                  <p className="text-sm">Te avisaremos por WhatsApp o correo en 24 horas.</p>
                </div>
              ) : (
                <form onSubmit={handleRegisterSubmit} className="space-y-4">
                  <div>
                    <label className="block text-xs font-semibold uppercase text-slate-600 mb-1">Nombre Comercial</label>
                    <input required type="text" className="w-full border rounded-lg p-2.5" onChange={e => setNewSchool({...newSchool, name: e.target.value})} />
                  </div>
                  <div className="grid grid-cols-2 gap-4">
                    <div>
                      <label className="block text-xs font-semibold uppercase text-slate-600 mb-1">Código DGT</label>
                      <input required type="text" className="w-full border rounded-lg p-2.5" onChange={e => setNewSchool({...newSchool, dgtCode: e.target.value})} />
                    </div>
                    <div>
                      <label className="block text-xs font-semibold uppercase text-slate-600 mb-1">NIF / CIF</label>
                      <input required type="text" className="w-full border rounded-lg p-2.5" onChange={e => setNewSchool({...newSchool, cif: e.target.value})} />
                    </div>
                  </div>
                  <button type="submit" className="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-lg mt-4">
                    Enviar a Verificación
                  </button>
                </form>
              )}
            </div>
          )}

          {activeTab === 'admin' && (
            <div>
              <h2 className="text-2xl font-bold mb-4">Panel Verificación Admin</h2>
              {pendingSchools.length === 0 ? (
                <div className="bg-white p-8 text-center rounded-xl border text-slate-500">Sin solicitudes pendientes.</div>
              ) : (
                pendingSchools.map(school => (
                  <div key={school.id} className="bg-white p-6 rounded-xl border flex justify-between items-center">
                    <div>
                      <h3 className="font-bold">{school.name}</h3>
                      <p className="text-xs text-slate-500">DGT: {school.dgtCode} | CIF: {school.cif}</p>
                    </div>
                    <div className="flex gap-2">
                      <button onClick={() => approveSchool(school.id)} className="bg-emerald-600 text-white text-xs px-4 py-2 rounded-lg">Aprobar</button>
                      <button onClick={() => rejectSchool(school.id)} className="bg-red-600 text-white text-xs px-4 py-2 rounded-lg">Rechazar</button>
                    </div>
                  </div>
                ))
              )}
            </div>
          )}

          {activeTab === 'billing' && (
            <div className="bg-white p-6 rounded-xl border">
              <h2 className="text-2xl font-bold mb-4">Facturación Integrada (Autónomo España)</h2>
              <p className="text-sm text-slate-600">Emisor de facturas: Bogdan Adrian Munteanu (NIE Y4827899Y). Todas las facturas llevan desglose automático del 21% de IVA.</p>
            </div>
          )}
        </main>
      </div>

      {/* FOOTER CON INFORMACIÓN LEGAL Y FISCAL OFICIAL */}
      <footer className="bg-slate-900 text-slate-400 py-8 border-t border-slate-800 text-sm">
        <div className="max-w-7xl mx-auto px-4 flex flex-col md:flex-row justify-between items-center gap-4">
          <div>
            <p className="font-bold text-white">AutoEscuelaCompare España</p>
            <p className="text-xs mt-1">Titular: Bogdan Adrian Munteanu | NIE: Y4827899Y</p>
            <p className="text-xs text-slate-500">Calle Veracruz 34, 6º 3ª, 28938 Móstoles, Madrid</p>
          </div>

          <div className="flex flex-wrap gap-6 text-xs">
            <button onClick={() => setActiveLegalModal('aviso')} className="hover:text-white underline flex items-center gap-1">
              <Scale className="w-3.5 h-3.5" /> Aviso Legal
            </button>
            <button onClick={() => setActiveLegalModal('privacidad')} className="hover:text-white underline">
              Política de Privacidad
            </button>
            <button onClick={() => setActiveLegalModal('cookies')} className="hover:text-white underline">
              Política de Cookies
            </button>
          </div>
        </div>
      </footer>

      {/* MODAL DE TEXTOS LEGALES */}
      {activeLegalModal && (
        <div className="fixed inset-0 bg-black/60 z-50 flex items-center justify-center p-4">
          <div className="bg-white max-w-2xl w-full max-h-[80vh] overflow-y-auto rounded-xl p-6 text-slate-800 relative">
            <button onClick={() => setActiveLegalModal(null)} className="absolute top-4 right-4 text-slate-500 hover:text-black">
              <X className="w-6 h-6" />
            </button>

            {activeLegalModal === 'aviso' && (
              <div>
                <h3 className="text-xl font-bold mb-3">Aviso Legal (LSSI-CE)</h3>
                <p className="text-sm leading-relaxed text-slate-600 space-y-2">
                  En cumplimiento del artículo 10 de la Ley 34/2002, de 11 de julio, de Servicios de la Sociedad de la Información y Comercio Electrónico (LSSI-CE):<br /><br />
                  <strong>Titular:</strong> Bogdan Adrian Munteanu<br />
                  <strong>NIE:</strong> Y4827899Y<br />
                  <strong>Domicilio Fiscal:</strong> Calle Veracruz nº 34, Piso 6, Puerta 3, 28938 Móstoles, Madrid, España<br />
                  <strong>Correo Electrónico:</strong> comparaautoescuelas@proton.me<br />
                  <strong>Teléfono / WhatsApp:</strong> +34 642 35 36 64
                </p>
              </div>
            )}

            {activeLegalModal === 'privacidad' && (
              <div>
                <h3 className="text-xl font-bold mb-3">Política de Privacidad (RGPD / LOPD-GDD)</h3>
                <p className="text-sm leading-relaxed text-slate-600">
                  <strong>Responsable:</strong> Bogdan Adrian Munteanu (NIE Y4827899Y).<br />
                  <strong>Finalidad:</strong> Gestión de la plataforma, verificación de autoescuelas y contacto.<br />
                  <strong>Derechos:</strong> Puedes ejercer tus derechos escribiendo a <em>comparaautoescuelas@proton.me</em>.
                </p>
              </div>
            )}

            {activeLegalModal === 'cookies' && (
              <div>
                <h3 className="text-xl font-bold mb-3">Política de Cookies</h3>
                <p className="text-sm leading-relaxed text-slate-600">
                  Este sitio web utiliza cookies técnicas de sesión imprescindibles para el funcionamiento de la plataforma.
                </p>
              </div>
            )}
          </div>
        </div>
      )}
    </div>
  );
}
