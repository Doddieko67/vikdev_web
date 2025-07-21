# 📊 Análisis Completo del Proyecto VikDev

> **Documento de Referencia** - Para mantener consistencia en desarrollo futuro  
> **Última actualización:** Enero 2025  
> **Autor:** Claude Code Analysis

---

## 🎨 **SISTEMA DE DISEÑO ESTABLECIDO**

### **Paleta de Colores Principal**
```css
/* Core Brand Colors */
--primary: #6366f1;        /* Índigo principal */
--secondary: #10b981;      /* Verde esmeralda */
--tertiary: oklch(51.1% 0.262 276.966);   /* Gradientes avanzados */
--quaternary: oklch(55.8% 0.288 302.321);

/* Sistema de Temas Dinámico */
--theme-bg-primary: #ffffff / #0f172a;     /* Fondo principal */
--theme-text-primary: #1e293b / #f8fafc;  /* Texto principal */
--theme-border-primary: #e2e8f0 / #334155; /* Bordes */
```

### **Componentes de Diseño Distintivos**

#### **1. Sistema de Paneles Deslizantes Cinematográficos**
- Transform/scale effects para transiciones
- Multiple layers con z-index hierarchy
- Blur effects para profundidad visual

#### **2. Cards Modernas con Estados**
- **RecentExamCard:** Gradientes de dificultad, badges de estado
- **ExamQuestionCard:** Iconografía clara, interacciones hover
- **Design Pattern:** Border radius 1rem, sombras suaves, spacing consistente

#### **3. Sistema de Iconografía**
- **Font Awesome** como estándar
- **Contextuales:** `fa-brain` (logo), `fa-robot` (IA), `fa-star` (favoritos)
- **Estados:** `fa-check-circle`, `fa-clock`, `fa-exclamation-triangle`

### **Tipografía y Microinteracciones**
- **Fuente:** "Poppins" (moderna, legible)
- **Transiciones:** 300ms standard
- **Hover effects:** Scale, shadow elevation
- **Animation:** Smooth, purposeful

---

## 🏗️ **ARQUITECTURA ESTABLECIDA**

### **Frontend Structure (`/frontend/src/`)**
```
src/
├── components/
│   ├── Main/              # Componentes página principal
│   ├── exam/              # Sistema de exámenes completo
│   ├── shared/            # Componentes reutilizables
│   └── UI/                # Elementos base de interfaz
├── pages/                 # Páginas principales
├── stores/                # Zustand state management
├── hooks/                 # Custom hooks especializados
├── services/              # API services layer
└── utils/                 # Utilities and helpers
```

### **Gestión de Estado - Zustand**
```typescript
interface AuthState {
  user: User | null;
  session: Session | null;
  loading: boolean;
  error: string | null;
  // Métodos async para auth
}
```
- **Persistencia:** localStorage integration
- **Supabase Auth:** Sesiones automáticas
- **Error handling:** Estados centralizados

### **Custom Hooks Especializados**
- `useExamState`: Estado completo de examen
- `useExamTimer`: Cronómetro con persistencia
- `useKeyboardNavigation`: Shortcuts (1-4, ←→)
- `useExamPersistence`: Auto-save functionality
- `useOfflineMode`: Funcionalidad offline

---

## 🧠 **FUNCIONALIDADES CORE IMPLEMENTADAS**

### **Sistema de Exámenes Inteligente**

#### **Generación con IA (3 modelos Gemini)**
```javascript
models = {
  "gemini-2.5-pro": "Análisis complejo",
  "gemini-2.5-flash": "Balance rendimiento/costo", 
  "gemini-2.0-flash": "Feedback rápido"
}
```

#### **Modos de Creación**
1. **Prompt Text:** Descripción libre → IA genera examen
2. **Upload Files:** PDF/Docs → IA analiza contenido
3. **Based on History:** Analiza patrones → Mejora áreas débiles

#### **Features Avanzadas**
- **Timer persistente:** Continúa tras refresh
- **Question pinning:** Marcar importantes
- **Keyboard navigation:** Full accessibility
- **Auto-save:** Cada respuesta guardada
- **Feedback IA:** Análisis post-examen personalizado

### **API Key Management**
- **Usuario gestiona su propia Gemini API key**
- **Encriptación AES-256** en base de datos
- **Validación previa** antes de usar IA
- **Fallback admin key** para casos de emergencia

---

## 🔧 **BACKEND ARCHITECTURE**

### **Express.js con Security Stack**
```javascript
// Middleware pipeline
app.use(securityHeaders);     // Helmet security headers
app.use(generalLimiter);      // Rate limiting inteligente
app.use(requestLogger);       // Winston logging
app.use(cors(corsOptions));   // CORS configurado
```

### **Rate Limiting por Operación**
```javascript
limits = {
  general: "100 requests/15min",
  aiOperations: "10 requests/min",    // Más restrictivo
  fileUpload: "5 requests/min"
}
```

### **Endpoints Principales**
```
POST /api/upload_files              # Upload + AI analysis  
POST /api/generate-content          # Text → AI exam
POST /api/generate-feedback         # Post-exam IA feedback
POST /api/gemini/configure-api-key  # User API key management
```

### **Database Schema (Supabase)**
```sql
-- API Keys con encriptación
user_api_keys (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id),
  service VARCHAR(50),  -- 'gemini'
  api_key TEXT,         -- AES-256 encrypted
  is_active BOOLEAN
)

-- Exámenes con datos JSON
examenes (
  id UUID,
  user_id UUID,
  datos JSONB,          -- Questions array
  respuestas_usuario JSONB,
  questions_pinned JSONB,
  feedback JSONB
)
```

---

## 📁 **PATTERNS PARA NUEVOS COMPONENTES**

### **Estructura de Component Típica**
```typescript
interface ComponentProps {
  user: User | null;
  onAction: (data: DataType) => void;
  className?: string;
}

export function Component({ user, onAction, className }: ComponentProps) {
  const [state, setState] = useState<StateType>(initialState);
  
  // Custom hooks
  const { data, loading, error } = useCustomHook();
  
  // Event handlers
  const handleAction = useCallback((e) => {
    // Logic here
  }, [dependencies]);

  return (
    <div className={`standard-container ${className}`}>
      {/* Component structure */}
    </div>
  );
}
```

### **Card Component Pattern**
```typescript
// Basado en RecentExamCard y ExamQuestionCard
<div className="bg-white dark:bg-gray-800 rounded-lg shadow border hover:shadow-lg transition-shadow">
  <div className="p-6">
    {/* Header con badges */}
    <div className="flex items-center justify-between mb-4">
      <h3 className="font-semibold text-gray-900 dark:text-white">Title</h3>
      <div className="flex gap-2">
        {/* Status badges */}
      </div>
    </div>
    
    {/* Content */}
    <div className="space-y-4">
      {/* Main content */}
    </div>
    
    {/* Footer actions */}
    <div className="flex justify-between items-center pt-4 border-t">
      {/* Action buttons */}
    </div>
  </div>
</div>
```

### **Form Pattern Standard**
```typescript
// Basado en formularios existentes
<form onSubmit={handleSubmit} className="space-y-6">
  <div>
    <label className="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
      Label *
    </label>
    <input
      type="text"
      required
      className="w-full px-4 py-2 border border-gray-300 dark:border-gray-600 rounded-lg bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
    />
  </div>
  
  <button
    type="submit"
    disabled={isSubmitting}
    className="w-full bg-blue-600 text-white py-3 px-6 rounded-lg font-medium hover:bg-blue-700 disabled:opacity-50 transition-colors"
  >
    {isSubmitting ? 'Loading...' : 'Submit'}
  </button>
</form>
```

---

## 🎯 **DATOS IMPORTANTES PARA TOMAR EN CUENTA**

### **✅ SÍ usar/seguir:**
- **Sistema de variables CSS** para temas
- **Componentes reutilizables** del directorio `/shared/`
- **Zustand** para estado global
- **Custom hooks** para lógica compleja
- **TypeScript strict** con tipos branded
- **Lazy loading** para componentes de página
- **React Query** para cache de API
- **Font Awesome** para iconografía
- **Tailwind utilities** con variables CSS
- **Winston logging** en backend
- **Rate limiting** en endpoints de IA
- **Supabase RLS** para seguridad

### **❌ NO implementar/evitar:**
- **Nuevos sistemas de estado** (usar Zustand existente)
- **Librerías de iconos diferentes** (mantener Font Awesome)
- **Sistemas de tema separados** (usar variables CSS existentes)
- **Frameworks CSS adicionales** (Tailwind es suficiente)
- **ORMs nuevos** (Supabase funciona bien)
- **Sistemas de auth propios** (Supabase Auth establecido)
- **Logging libraries diferentes** (Winston ya configurado)
- **Bundlers diferentes** (Vite optimizado)

### **🔄 Patrones a mantener:**
1. **Lazy loading** de páginas principales
2. **Error boundaries** para components críticos  
3. **Loading states** consistentes en toda la app
4. **Responsive design** mobile-first
5. **Dark mode** por defecto en todos los components
6. **Accessibility** con ARIA labels apropiados
7. **Performance** con memo/callback optimizations

---

## 📝 **CHECKLIST PARA NUEVOS FEATURES**

### **Antes de empezar:**
- [ ] Revisar componentes similares existentes
- [ ] Identificar hooks reutilizables 
- [ ] Definir tipos TypeScript apropiados
- [ ] Planear integración con estado global

### **Durante desarrollo:**
- [ ] Usar variables CSS para temas
- [ ] Implementar lazy loading si es página
- [ ] Añadir loading y error states
- [ ] Incluir responsive design
- [ ] Agregar accessibility attributes
- [ ] Documentar props y tipos

### **Antes de commit:**
- [ ] Build exitoso sin warnings
- [ ] Componente funciona en dark/light mode
- [ ] Responsive en móvil y desktop
- [ ] Performance optimizada (memo/callback)
- [ ] TypeScript sin errores
- [ ] Consistente con design system

---

## 🚀 **TECNOLOGÍAS Y VERSIONES**

### **Frontend Stack**
- **React 19** + **TypeScript 5**
- **Tailwind CSS 4.x** + **Vite 6**
- **Zustand 5** + **React Query**
- **React Router 7** + **Supabase Client**

### **Backend Stack**  
- **Node.js 18+** + **Express 5**
- **Supabase** (Auth + PostgreSQL)
- **Google Gemini AI** + **Winston Logger**
- **Joi validation** + **Multer uploads**

### **Production Config**
```
Environment: production
Domain: vikdev.dev
API: server.vikdev.dev  
CDN: Cloudflare
Database: Supabase (PostgreSQL)
Auth: Supabase Auth
Storage: Supabase Storage
```

---

## 💡 **PHILOSOPHY & PRINCIPLES**

> **"Focus en funcionalidad útil sobre features flashy"**  
> **"Consistencia en UX > Innovación visual"**  
> **"Performance y accessibility como prioridad"**  
> **"Código mantenible y escalable"**

**El proyecto VikDev está muy bien estructurado y optimizado. Cualquier nueva funcionalidad debe mantener estos estándares de calidad y consistencia establecidos.**