## 🚀 AI Chatbot Upgrade - Developer Quick Start

### What Changed?

Your chatbot went from **rule-based** → **AI-powered** while keeping all existing features.

---

## 📂 New Files Created

```
src/lib/
  ├── recommendSchemes.ts    (NEW) - Rule-based filtering
  └── aiEngine.ts            (NEW) - AI explanations & logic

src/components/
  └── SchemeResults.tsx      (NEW) - Beautiful results UI
```

---

## 🔄 How It Works Now

### Old Flow
```
User answers 5 questions → Basic if/else logic → Show results
```

### New Flow
```
User answers 5 questions 
  ↓
recommendSchemes() → Check eligibility (fast)
  ↓
generateAIResponse() → AI explanations (async)
  ↓
SchemeResults component → Show beautiful UI
```

---

## 🎯 Using It in Your Code

### Example 1: Get Recommendations
```typescript
import { recommendSchemes } from "@/lib/recommendSchemes";

const results = recommendSchemes({
  age: "25",
  occupation: "farmer",
  income: "500000",
  landOwnership: "yes",
  category: "general"
});

// Results include eligible, matchScore, successCriteria, failedCriteria
results.forEach(r => {
  console.log(`${r.scheme.name}: ${r.matchScore}% match`);
});
```

### Example 2: Get AI Explanations
```typescript
import { generateAIResponse } from "@/lib/aiEngine";
import { recommendSchemes, getSortedRecommendations } from "@/lib/recommendSchemes";

const userProfile = { age: "25", occupation: "farmer", ... };
const results = recommendSchemes(userProfile);
const sorted = getSortedRecommendations(results);

const aiResponse = await generateAIResponse(userProfile, sorted, "en");

console.log(aiResponse.summary);
// "Great news! You are eligible for 3 schemes..."

console.log(aiResponse.prioritizedSchemes);
// Top 3 recommendations with reasons
```

### Example 3: Display Results
```typescript
import { SchemeResults } from "@/components/SchemeResults";

<SchemeResults 
  aiResponse={aiResponse}
  language={language}
  onApply={(schemeId) => navigate(`/apply/${schemeId}`)}
/>
```

---

## 🧪 Quick Testing

### Test in Browser Console
```javascript
// Import the functions
async function testRecommendations() {
  const { recommendSchemes, getSortedRecommendations } = await import('./lib/recommendSchemes.ts');
  const { generateAIResponse } = await import('./lib/aiEngine.ts');
  
  const profile = {
    age: "60",
    occupation: "laborer",
    income: "150000",
    landOwnership: "no",
    category: "general"
  };
  
  const results = recommendSchemes(profile);
  const sorted = getSortedRecommendations(results);
  const aiResponse = await generateAIResponse(profile, sorted, "en");
  
  console.log(aiResponse);
}

testRecommendations();
```

---

## 🎛️ Configuration Points

### 1. Change Scheme Eligibility Logic

File: `src/lib/recommendSchemes.ts`

```typescript
case "ysr-rhythu-bharosa": {
  // Modify these conditions
  if (occupation === "farmer") matchingCriteria.push("Occupation matches");
  else failingCriteria.push("Not a farmer");

  // Adjust income threshold
  if (income < 1000000) matchingCriteria.push("Income below ₹10 lakhs");
  else failingCriteria.push("Income exceeds ₹10 lakhs");
  
  // Add/remove criteria as needed
  break;
}
```

### 2. Change AI Explanations

File: `src/lib/aiEngine.ts`

```typescript
function generateExplanation(result, userProfile, language) {
  // Modify explanation text here
  const explanation = language === "en"
    ? `Your custom explanation text...`
    : `Your Telugu explanation...`;
  
  return { explanation, missedOpportunity };
}
```

### 3. Change Priority Ranking

File: `src/utils/apSchemes.ts`

```typescript
{
  id: "my-scheme",
  name: "My Scheme",
  fundingAmount: "₹50,000/year",
  benefitAmount: 50000,
  priority: "high", // Change this: high/medium/low
  apSpecific: true
}
```

### 4. Update Colors/Styling

File: `src/components/SchemeResults.tsx`

```typescript
className={`border rounded-lg p-4 ${
  scheme.eligible
    ? "border-stat-green bg-stat-green/5" // Change these colors
    : "border-border bg-muted/30"
}`}
```

---

## 🔌 Connecting a Real LLM

### Option 1: OpenAI
```typescript
// In aiEngine.ts
import OpenAI from "openai";

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

export async function generateAIResponse(userProfile, results, language) {
  const prompt = `Analyze this user profile and scheme eligibility:
  
User: Age ${userProfile.age}, ${userProfile.occupation}, Income ₹${userProfile.income}

Schemes:
${results.map(r => `- ${r.scheme.name}: ${r.matchScore}% match`).join('\n')}

Provide:
1. Summary of eligibility
2. Top 3 recommendations
3. Next steps for each scheme

Language: ${language === 'te' ? 'Telugu' : 'English'}`;

  const response = await openai.chat.completions.create({
    model: "gpt-4",
    messages: [{ role: "user", content: prompt }]
  });

  return parseResponse(response.choices[0].message.content);
}
```

### Option 2: Anthropic Claude
```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic();

const message = await client.messages.create({
  model: "claude-3-sonnet-20240229",
  max_tokens: 1024,
  messages: [{ role: "user", content: prompt }]
});
```

### Option 3: Local API Endpoint
```typescript
const response = await fetch('/api/ai/explain', {
  method: 'POST',
  body: JSON.stringify({ userProfile, results, language })
});

const aiResponse = await response.json();
```

---

## 📊 How Match Score Is Calculated

```
Match Score = (Criteria Met / Total Criteria) × 100

Example:
Scheme needs: Occupation + Income + Land ownership
User matches: Occupation ✓ + Income ✓ + Land ownership ✗

Match Score = 2/3 × 100 = 67%
```

---

## 🎨 Customizing the UI

### Change Result Colors

```typescript
// High match = eligible
className="border-stat-green bg-stat-green/5"

// Medium match = almost eligible  
className="border-stat-orange bg-stat-orange/5"

// Low match = not eligible
className="border-border bg-muted/30"
```

### Add More Status Badges

```typescript
{scheme.eligible ? (
  <span className="bg-stat-green text-white">✓ Eligible</span>
) : scheme.matchScore >= 66 ? (
  <span className="bg-stat-orange text-white">⚠ Almost Eligible</span>
) : (
  <span className="bg-gray-300 text-gray-800">✗ Not Eligible</span>
)}
```

---

## 🐛 Debugging Tips

### Enable Verbose Logging
```typescript
// In Chatbot.tsx
const recommendationMutation = useMutation({
  mutationFn: async () => {
    const rules = recommendSchemes(userProfile);
    console.log("Rule-based results:", rules);
    
    const sorted = getSortedRecommendations(rules);
    console.log("Sorted results:", sorted);
    
    const ai = await generateAIResponse(userProfile, sorted, language);
    console.log("AI response:", ai);
    
    return ai;
  }
});
```

### Test Missed Opportunity Detection
```typescript
import { findMissedOpportunities } from "@/lib/recommendSchemes";

const results = recommendSchemes(userProfile);
const closeShaves = findMissedOpportunities(results);

console.log("Almost eligible for:", closeShaves.map(r => r.scheme.name));
```

### Check Match Scores
```typescript
const results = recommendSchemes(userProfile);

results.forEach(r => {
  console.log(`${r.scheme.name}:`);
  console.log(`  Match: ${r.matchScore}%`);
  console.log(`  Met: ${r.successCriteria?.join(", ")}`);
  console.log(`  Missing: ${r.failedCriteria?.join(", ")}`);
});
```

---

## ⚡ Performance Tips

### Cache Results
```typescript
const resultsCache = new Map();

function getRecommendations(profile) {
  const key = JSON.stringify(profile);
  
  if (resultsCache.has(key)) {
    return resultsCache.get(key);
  }
  
  const results = recommendSchemes(profile);
  resultsCache.set(key, results);
  return results;
}
```

### Lazy Load AI Response
```typescript
// Don't await AI if not immediately needed
const rulesTask = recommendSchemes(userProfile);
const aiTask = generateAIResponse(userProfile, rulesTask);

// Display rules first, AI explanations second
```

---

## 📝 Adding More Schemes

### Step 1: Update `apSchemes.ts`
```typescript
{
  id: "new-scheme-id",
  name: "New Scheme Name",
  category: "Healthcare",
  fundingAmount: "₹15,000/year",
  benefitAmount: 15000,
  priority: "high",
  eligibility: ["Criteria 1", "Criteria 2"],
  benefits: ["Benefit 1", "Benefit 2"],
  // ... other fields
}
```

### Step 2: Add Eligibility Logic
```typescript
// In recommendSchemes.ts
case "new-scheme-id": {
  if (occupation === "targetOccupation") {
    matchingCriteria.push("Occupation matches");
  }
  // Add more conditions
  break;
}
```

### Step 3: Update Language Support
```typescript
// In language.ts
schemes: {
  new_scheme_id: "New Scheme Name",
  // ...
}
```

---

## 🚀 Deployment Checklist

- [ ] Run `npm run build` - no errors
- [ ] Run `npm run dev` - app loads
- [ ] Test chatbot with 5 different profiles
- [ ] Verify Telugu translations
- [ ] Check "Apply Now" button navigation
- [ ] Test mobile responsive layout
- [ ] Verify dark mode if applicable
- [ ] Check browser console for errors
- [ ] Test language toggle
- [ ] Verify "Check Again" resets properly

---

## 📞 Common Issues

### Issue: "Module not found"
**Solution:** Check import paths use `@/lib/` notation
```typescript
// ✓ Correct
import { recommendSchemes } from "@/lib/recommendSchemes";

// ✗ Wrong
import { recommendSchemes } from "./lib/recommendSchemes";
```

### Issue: Types not exported
**Solution:** Add `export` keyword to interfaces
```typescript
// ✓ Correct
export interface RecommendationResult { ... }

// ✗ Wrong
interface RecommendationResult { ... }
```

### Issue: Chatbot loads but shows white screen
**Solution:** Check SchemeResults component renders properly
```typescript
// Verify SchemeResults receives aiResponse
{state.aiResponse ? (
  <SchemeResults aiResponse={state.aiResponse} ... />
) : null}
```

### Issue: Match scores are always 0 or 100
**Solution:** Check eligibility conditions in recommendSchemes.ts
```typescript
// Make sure you're checking actual criteria
if (income < 500000) matchingCriteria.push("Income check");
// Not just always adding criteria
```

---

## 📚 Reference

### Key Exports
```typescript
// recommendSchemes.ts
export { recommendSchemes, getSortedRecommendations, findMissedOpportunities }
export type { RecommendationResult, UserProfile }

// aiEngine.ts
export { generateAIResponse, formatAIResponse }
export type { AIResponse, AIEnhancedResult }

// SchemeResults.tsx
export { SchemeResults }
```

### Interfaces Reference
```typescript
UserProfile = { age, occupation, income, landOwnership, category }

RecommendationResult = {
  scheme,          // Full Scheme object
  eligible,        // Boolean
  matchScore,      // 0-100
  successCriteria, // String[]
  failedCriteria   // String[]
}

AIResponse = {
  summary,
  telugu_summary,
  results,           // AIEnhancedResult[]
  prioritizedSchemes, // Top 3 recommendations
  nextSteps
}
```

---

## 🎓 Learning Resources

- **TypeScript:** https://www.typescriptlang.org/docs/
- **React Hooks:** https://react.dev/reference/react/hooks
- **React Router:** https://reactrouter.com/
- **React Query:** https://tanstack.com/query/latest
- **Tailwind CSS:** https://tailwindcss.com/docs

---

**Version:** 1.0  
**Last Updated:** April 4, 2026  
**Status:** ✅ Ready for Development

Happy coding! 🚀
