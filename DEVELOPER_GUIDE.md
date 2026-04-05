# 🚀 AP Welfare Assistant - Developer Quick Start Guide

## Project Overview

This is an **AI-powered Telugu Welfare Assistant for Andhra Pradesh** built with React 18, TypeScript, and modern web technologies.

**Key Feature:** A floating ChatBot on every page that intelligently recommends AP government schemes based on user profile.

---

## 🛠️ Development Setup

### Prerequisites
- Node.js 18+
- npm or bun

### Installation

```bash
# Navigate to project directory
cd c:\Users\nagir\Desktop\government_2

# Install dependencies
npm install

# Start development server
npm run dev

# Server runs at http://localhost:8080
```

---

## 📁 Project Structure

```
src/
├── components/
│   ├── Chatbot.tsx                 # 🤖 AI Chatbot (global)
│   ├── HeroSection.tsx             # Hero with AP branding
│   ├── SchemesSection.tsx          # Featured schemes
│   ├── StatsSection.tsx            # Live statistics
│   └── [other UI components]
├── pages/
│   ├── Index.tsx                   # Home page
│   ├── SchemesPage.tsx             # All schemes (searchable)
│   ├── SchemeDetailPage.tsx        # Scheme details
│   ├── ApplicationFormPage.tsx      # Apply form
│   ├── TrackApplicationPage.tsx     # Track status
│   └── DashboardPage.tsx           # User dashboard
├── services/
│   └── api.ts                      # API layer (React Query)
├── utils/
│   ├── language.ts                 # 🌍 EN/TE translations
│   └── apSchemes.ts                # 📋 Scheme data
├── types/
│   └── index.ts                    # TypeScript interfaces
├── App.tsx                         # Main app + routing
└── main.tsx                        # Entry point
```

---

## 🤖 Chatbot Integration (Global)

### How It Works:
The chatbot is integrated globally in `App.tsx`:

```typescript
// App.tsx
import Chatbot from "@/components/Chatbot";

const App = () => (
  <QueryClientProvider {...}>
    <BrowserRouter>
      <Chatbot />  {/* 🎯 Appears on ALL pages */}
      <Routes>
        {/* Your routes */}
      </Routes>
    </BrowserRouter>
  </QueryClientProvider>
);
```

### Component Location:
📄 `src/components/Chatbot.tsx` (280+ lines)

### Chatbot Features:
- ✅ Floating button (bottom-right)
- ✅ Modal chat interface
- ✅ 5-step questionnaire
- ✅ Bilingual (English/Telugu)
- ✅ Smart scheme recommendations
- ✅ Direct "Apply" navigation

---

## 📋 Scheme Data

### Data Source:
📄 `src/utils/apSchemes.ts`

### Available Schemes (6 in total):
```typescript
const apSchemes: Scheme[] = [
  {
    id: "ysr-rhythu-bharosa",
    name: "YSR Rythu Bharosa",
    category: "Agriculture",
    telugu_description: "Telugu translation here...",
    eligibility: [...],
    benefits: [...]
  },
  // ... 5 more schemes
];
```

### Adding a New Scheme:

```typescript
// src/utils/apSchemes.ts
{
  id: "new-scheme-id",
  name: "New Scheme Name",
  shortDesc: "English description",
  telugu_description: "తెలుగు వివరణ",
  icon: "IconName",  // from lucide-react
  color: "border-stat-green",
  iconColor: "text-stat-green",
  category: "Agriculture",  // or Education, Healthcare, etc.
  eligibility: [
    "Criteria 1",
    "Criteria 2"
  ],
  benefits: [
    "Benefit 1",
    "Benefit 2"
  ],
  fundingAmount: "₹10,000",
  applicationDeadline: "2026-12-31",
  apSpecific: true
}
```

---

## 🌍 Language Support (Telugu)

### Current Status:
- ✅ English (default)
- ✅ Telugu (తెలుగు)

### Add New Language:

```typescript
// src/utils/language.ts
export const kannada = {
  chatbotGreeting: "ಸ್ವಾಗತ!",
  questions: {
    age: "ನಿಮ್ಮ ವಯಸ್ಸು ಎಷ್ಟು?",
    // ... more keys
  },
  // ... rest of translations
};

export const getLanguage = (lang: 'te' | 'en' | 'kn' = 'en') => {
  switch(lang) {
    case 'te': return telugu;
    case 'kn': return kannada;
    default: return english;
  }
};
```

---

## 🔍 Chatbot Recommendation Logic

### Current Logic:
Located in `src/components/Chatbot.tsx` (lines ~100-160)

### Example: YSR Rythu Bharosa
```typescript
if (userData.occupation === "farmer" && 
    parseInt(userData.income) < 1000000) {
  recommendations.push({
    id: "ysr-rhythu-bharosa",
    name: lang.schemes.ysr_rhythu_bharosa,
    eligible: true,
    reason: "You are a farmer with income below ₹10 lakhs"
  });
}
```

### Update Recommendations:
1. Edit the logic in `Chatbot.tsx` → `getRecommendations()` function
2. Match user data against scheme eligibility
3. Return array of eligible schemes with reasons

---

## 📱 Responsive Design

All components are mobile-first responsive:
- Mobile: 1 column
- Tablet: 2 columns
- Desktop: 3+ columns

### Tailwind Classes Used:
```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {/* Responsive grid */}
</div>
```

---

## 🎨 Styling System

### Color Palette (AP Government Colors):
```css
--gov-blue: #003f87
--gov-orange: #FF6100
--gov-saffron: #FF9933
--stat-green: #10B981
--stat-orange: #F97316
```

### Components:
Using Shadcn UI + Tailwind CSS for consistent styling.

---

## 📊 React Query Usage

### Fetching Schemes:
```typescript
// In any component
const { data: schemes, isLoading } = useQuery({
  queryKey: ["schemes"],
  queryFn: () => schemeApi.getAllSchemes(),
});
```

### Available API Endpoints:
```typescript
// src/services/api.ts
schemeApi.getAllSchemes()        // Get all schemes
schemeApi.getSchemeById(id)      // Get single scheme
schemeApi.searchSchemes(query)   // Search schemes
schemeApi.getSchemesByCategory() // Filter by category

applicationApi.submitApplication()
applicationApi.getUserApplications()
applicationApi.trackApplication()

userApi.getCurrentUser()
userApi.updateUserProfile()
```

---

## 🚀 Deployment

### Build for Production:
```bash
npm run build
# Creates optimized dist/ folder

npm run preview
# Preview production build locally
```

### Environment Variables:
Create `.env` file:
```
VITE_API_BASE_URL=https://api.example.com
```

---

## ✅ Testing

### Current Test Files:
- `test/example.test.ts`
- `test/setup.ts`

### Run Tests:
```bash
npm run test          # Run tests once
npm run test:watch    # Watch mode
```

### Test Chatbot:
1. Visit any page
2. Click floating blue button
3. Answer questions
4. Verify scheme recommendations

---

## 🐛 Common Issues & Solutions

### Issue: Chatbot not appearing
**Solution:** Check if `<Chatbot />` is imported in `App.tsx`

### Issue: Schemes not loading
**Solution:** Verify `apSchemes.ts` is importing correctly
```typescript
import { apSchemes } from "@/utils/apSchemes";
```

### Issue: Language not switching
**Solution:** Check language state in Chatbot component
```typescript
const [language, setLanguage] = useState<"en" | "te">("en");
```

---

## 📚 Key TypeScript Interfaces

### Scheme
```typescript
interface Scheme {
  id: string;
  name: string;
  category: string;
  eligibility: string[];
  benefits: string[];
  fundingAmount?: string;
  apSpecific?: boolean;
  telugu_description?: string;
  // ... more fields
}
```

### Application
```typescript
interface Application {
  id: string;
  userId: string;
  schemeId: string;
  status: 'pending' | 'approved' | 'rejected' | 'submitted';
  applicationNumber: string;
  // ... more fields
}
```

---

## 🔗 API Integration (Ready for Backend)

### Future Backend Endpoint:
```
POST /api/recommend
Content-Type: application/json

{
  "age": "25",
  "occupation": "farmer",
  "income": "500000",
  "landOwnership": "yes",
  "category": "general"
}

Response: Array of compatible schemes
```

### Update API Call:
```typescript
// src/services/api.ts
const recommendationMutation = useMutation({
  mutationFn: async (data) => {
    const response = await axios.post('/api/recommend', data);
    return response.data;
  }
});
```

---

## 🎯 Next Steps (for Developers)

### Phase 1: Current Implementation ✅
- [x] Chatbot UI
- [x] AP schemes data
- [x] Telugu support
- [x] Recommendation logic

### Phase 2: Enhanced Features
- [ ] Backend API integration
- [ ] User authentication
- [ ] Database for applications
- [ ] Email/SMS notifications
- [ ] Payment processing

### Phase 3: Advanced Features
- [ ] Voice input (Web Speech API)
- [ ] AI/ML recommendations
- [ ] WhatsApp bot integration
- [ ] Admin dashboard
- [ ] Analytics

---

## 📝 Code Style

### Naming Conventions:
- Components: PascalCase (`Chatbot.tsx`)
- Functions: camelCase (`getRecommendations`)
- Constants: UPPER_SNAKE_CASE (`API_BASE_URL`)
- Variables: camelCase (`schemeData`)

### Component Structure:
```typescript
import { hooks } from "@/utils";
import { Component } from "@/components";

const MyComponent: React.FC<Props> = () => {
  // State
  const [state, setState] = useState();
  
  // Queries/Mutations
  const query = useQuery({...});
  
  // Handlers
  const handleClick = () => {};
  
  // Render
  return <div>content</div>;
};

export default MyComponent;
```

---

## 🤝 Contributing

1. Create a feature branch
2. Make changes
3. Test locally (`npm run dev`)
4. Commit with clear messages
5. Push and create PR

---

## 📞 Support

For issues or questions:
1. Check documentation first
2. Review component code
3. Check console for errors
4. Verify data in `apSchemes.ts`

---

## 📝 Useful Links

- [React Documentation](https://react.dev)
- [React Router Docs](https://reactrouter.com)
- [React Query Docs](https://tanstack.com/query)
- [Tailwind CSS Docs](https://tailwindcss.com)
- [Shadcn UI Components](https://ui.shadcn.com)
- [Lucide Icons](https://lucide.dev)

---

## ⚡ Performance Tips

1. **Use React.memo** for expensive components
2. **Lazy load pages** with React.lazy()
3. **Cache API responses** with React Query
4. **Optimize images** (use WebP format)
5. **Bundle analysis** with `npm run build` + webpack analyzer

---

## 🔒 Security Notes

1. Never expose API keys in frontend
2. Validate all user input
3. Use HTTPS for API calls
4. Sanitize user-generated content
5. Implement CSRF tokens for forms

---

## 📊 Project Stats

- **Total React Components:** 15+
- **Total Pages:** 7
- **Chatbot Questions:** 5
- **AP Schemes:** 6
- **Languages:** 2 (English, Telugu)
- **Lines of Code:** ~5000+
- **TypeScript Files:** ~25+

---

**Happy Coding! 🎉**

For the latest version, check the documentation files:
- `PROJECT_SUMMARY.md` - Project overview
- `AP_TRANSFORMATION.md` - Complete transformation details

---

Version: 1.0  
Last Updated: April 4, 2026

