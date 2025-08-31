6/* Complete Dating App Project: Let's Do This Includes Mobile + Web Frontend, Supabase Backend, Stripe Integration, and Active AI Chat Bots */

// --------------------------- // 1. Shared Config // --------------------------- export const SUPABASE_URL = 'https://your-supabase-url.supabase.co'; export const SUPABASE_ANON_KEY = 'your-anon-key'; export const STRIPE_PUBLIC_KEY = 'your-stripe-public-key'; export const OPENAI_API_KEY = 'your-openai-api-key';

// --------------------------- // 2. Supabase Client Setup // --------------------------- import { createClient } from '@supabase/supabase-js'; export const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// --------------------------- // 3. Mobile App (React Native / Expo) // --------------------------- // App.js import React from 'react'; import { NavigationContainer } from '@react-navigation/native'; import { createStackNavigator } from '@react-navigation/stack'; import LoginScreen from './screens/LoginScreen'; import HomeScreen from './screens/HomeScreen'; import ChatScreen from './screens/ChatScreen';

const Stack = createStackNavigator();

export default function App() { return ( <NavigationContainer> <Stack.Navigator initialRouteName="Login"> <Stack.Screen name="Login" component={LoginScreen} /> <Stack.Screen name="Home" component={HomeScreen} /> <Stack.Screen name="Chat" component={ChatScreen} /> </Stack.Navigator> </NavigationContainer> ); }

// --------------------------- // 4. Web App (React + Tailwind) // --------------------------- // index.js import React from 'react'; import ReactDOM from 'react-dom'; import App from './App'; import './index.css';

ReactDOM.render(<App />, document.getElementById('root'));

// App.js (web) import React from 'react'; import { BrowserRouter as Router, Route, Routes } from 'react-router-dom'; import LoginPage from './pages/LoginPage'; import HomePage from './pages/HomePage'; import ChatPage from './pages/ChatPage';

export default function App() { return ( <Router> <Routes> <Route path="/" element={<LoginPage />} /> <Route path="/home" element={<HomePage />} /> <Route path="/chat/:matchId" element={<ChatPage />} /> </Routes> </Router> ); }

// --------------------------- // 5. AI Chat Bot Logic // --------------------------- import axios from 'axios'; import { supabase } from '../shared/config';

export async function botSendMessage(botId, userId) { // Fetch last user message const { data: messages } = await supabase .from('messages') .select('*') .eq('match_id', await getMatchId(botId, userId)) .order('timestamp', { ascending: false }) .limit(1);

const lastUserMessage = messages[0]?.content || '';

// Generate AI reply const response = await axios.post( 'https://api.openai.com/v1/chat/completions', { model: 'gpt-3.5-turbo', messages: [{ role: 'user', content: lastUserMessage }], }, { headers: { Authorization: Bearer ${process.env.OPENAI_API_KEY} }, } );

const botMessage = response.data.choices[0].message.content;

// Insert bot message into Supabase await supabase.from('messages').insert({ match_id: await getMatchId(botId, userId), sender_id: botId, content: botMessage, timestamp: new Date(), }); }

async function getMatchId(botId, userId) { const { data: match } = await supabase .from('matches') .select('*') .or(user1_id.eq.${botId},user2_id.eq.${botId}) .eq('status', 'matched') .single();

return match?.id; }

// --------------------------- // 6. Stripe Subscription Integration // --------------------------- import { loadStripe } from '@stripe/stripe-js'; const stripePromise = loadStripe(STRIPE_PUBLIC_KEY);

export async function subscribeUser(priceId) { const stripe = await stripePromise; const { data } = await supabase.functions.invoke('create-stripe-session', { priceId }); await stripe.redirectToCheckout({ sessionId: data.sessionId }); }

// --------------------------- // 7. Swipe / Match Logic // --------------------------- export async function swipe(userId, targetId, action) { if (action === 'like') { // Check if target liked back const { data: match } = await supabase .from('matches') .select('*') .or(user1_id.eq.${targetId},user2_id.eq.${targetId}) .eq('status', 'pending') .single();

if (match) {
  await 
