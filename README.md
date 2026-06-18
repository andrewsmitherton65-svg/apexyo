# apexyo
import { useState, useEffect, useRef } from "react";

/* ─────────────────────────── DATA ─────────────────────────── */
const SPORTS = [
  { name:"Wrestling",    emoji:"🤼" },
  { name:"Football",     emoji:"🏈" },
  { name:"Basketball",   emoji:"🏀" },
  { name:"Baseball",     emoji:"⚾" },
  { name:"Soccer",       emoji:"⚽" },
  { name:"Golf",         emoji:"⛳" },
  { name:"Track & Field",emoji:"🏃" },
  { name:"Volleyball",   emoji:"🏐" },
  { name:"Tennis",       emoji:"🎾" },
  { name:"Swimming",     emoji:"🏊" },
  { name:"Cross Country",emoji:"🌲" },
  { name:"Gymnastics",   emoji:"🤸" },
  { name:"Lacrosse",     emoji:"🥍" },
  { name:"Hockey",       emoji:"🏒" },
  { name:"Other",        emoji:"🏅" },
];

const TABS = [
  { id:"home",    icon:"⌂",  label:"HOME"    },
  { id:"coach",   icon:"◎",  label:"COACH"   },
  { id:"train",   icon:"△",  label:"TRAIN"   },
  { id:"journal", icon:"≡",  label:"LOG"     },
  { id:"profile", icon:"○",  label:"YOU"     },
];

const TRAIN_MODULES = [
  { id:"breathe",   icon:"◎", label:"Breathe",   color:"#06b6d4", desc:"Calm your nervous system"    },
  { id:"meditate",  icon:"◯", label:"Meditate",   color:"#8b5cf6", desc:"Guided mental sessions"      },
  { id:"visualize", icon:"◈", label:"Visualize",  color:"#fb923c", desc:"Mental rehearsal"            },
  { id:"routine",   icon:"▷", label:"Pre-Game",   color:"#ef4444", desc:"Activation sequence"         },
  { id:"goals",     icon:"◇", label:"Goals",      color:"#22c55e", desc:"Set & track your targets"    },
  { id:"recovery",  icon:"↺", label:"Recovery",   color:"#a78bfa", desc:"Bounce back stronger"        },
];

const BREATH_PATTERNS = [
  { name:"Box Breathing", inhale:4, hold1:4, exhale:4, hold2:4, color:"#06b6d4", tag:"FOCUS",  desc:"Navy SEAL technique for calm focus" },
  { name:"4-7-8 Calm",    inhale:4, hold1:7, exhale:8, hold2:0, color:"#8b5cf6", tag:"CALM",   desc:"Rapid anxiety reduction"           },
  { name:"Power Breath",  inhale:5, hold1:2, exhale:3, hold2:0, color:"#ef4444", tag:"ENERGY", desc:"Pre-competition energizer"         },
];

const MEDITATIONS = [
  { id:"confidence", icon:"▲", title:"Confidence Surge",  duration:180, tag:"DAILY",   color:"#fb923c",
    script:["Settle in. Feel the weight of your body. You are grounded.","Recall your greatest performance moment. See every detail — the court, the field, the mat.","Feel how your body moved. Strong. Precise. Completely in flow.","That is not a memory. That is your baseline. That athlete is who you are.","Breathe that confidence into your chest, your arms, your legs. It is alive in you right now.","You are prepared. You have earned this. Go take what's yours."] },
  { id:"pregame",    icon:"▷", title:"Pre-Competition",   duration:120, tag:"GAME DAY", color:"#ef4444",
    script:["Three slow breaths. Release everything outside this moment.","Picture your competition ahead. See yourself arriving, warm, ready, sharp.","Every scenario that appears — you've trained for it. Your body knows exactly what to do.","Stay process. Stay present. One action, one moment at a time.","You don't need to be perfect. You need to be you — fully committed. Do that, and the result takes care of itself."] },
  { id:"recovery",   icon:"↺", title:"After a Loss",      duration:240, tag:"RECOVERY", color:"#38bdf8",
    script:["Be still. Let yourself feel this without judgment.","A loss is data. Not a verdict. What did this competition reveal?","Every champion you admire has sat exactly where you are sitting right now.","Your identity is not your scoreboard. You are more than any single result.","What is the one thing this loss is teaching you? Lock that in. That's your edge now.","This moment — not the loss, but your response to it — is where champions are made."] },
  { id:"sleep",      icon:"◐", title:"Sleep & Recovery",  duration:300, tag:"NIGHT",    color:"#8b5cf6",
    script:["Let your body become heavy. Sink into the surface beneath you.","Scan from your toes upward. Release every muscle as you go.","Your training today is complete. Your body is already rebuilding. Getting stronger.","Thoughts may appear. Let them pass. You don't need to solve anything tonight.","Rest is not passive. Sleep is the most important training session you'll have today.","Drift into deep, restorative sleep. Tomorrow you'll be better than today."] },
];

const VISUALIZATIONS = [
  { id:"peak",     icon:"◈", title:"Peak Performance",  color:"#fb923c", prompt:"Create a vivid guided visualization for a {sport} athlete experiencing their ideal peak performance. Cover the warm-up feeling, first moments of competition, a challenging moment they overcome, and finishing strong. Use present tense, second person. Be specific to {sport}. 5 paragraphs." },
  { id:"anchor",   icon:"▲", title:"Confidence Anchor", color:"#ef4444", prompt:"Guide a {sport} athlete through vividly reliving their greatest athletic achievement. Anchor it as a mental resource they can access instantly before competition. Sensory-rich, second person, present tense. 4 paragraphs." },
  { id:"goal",     icon:"◇", title:"Goal Achievement",  color:"#22c55e", prompt:"Guide a {sport} athlete through a visualization of having achieved their biggest athletic goal 6 months from now. What does it feel like? What did they do to get there? Make it emotional and specific. 4 paragraphs." },
  { id:"adversity",icon:"◎", title:"Handle Adversity",  color:"#38bdf8", prompt:"Guide a {sport} athlete through visualizing a tough moment in competition — then responding with composure, adjusting, and performing through it. Show the mental reset process. 4 paragraphs." },
];

function getSportContext(sport) {
  const map = {
    Wrestling:     "weight cutting stress, mat anxiety, one-on-one pressure, making weight, bracket draws, overtime situations",
    Football:      "QB reads under pressure, OL assignment execution, play-off mistakes, film study mindset, injury anxiety, big game nerves",
    Basketball:    "free throw yips, shooting slumps, foul trouble mindset, clutch moment pressure, bench-to-starter transitions",
    Baseball:      "batting slumps, pitcher-batter mind games, error recovery, long season mental stamina, walk-up routine importance",
    Soccer:        "penalty kick pressure, missed scoring chances, defensive breakdowns, 90-minute sustained focus, set piece nerves",
    Golf:          "back nine choking, pre-shot routine breakdown, anger after bad holes, course management thinking, tournament pressure",
    "Track & Field":"race day anxiety, start block nerves, PR pressure, heat/lane assignments, pacing judgment",
    Volleyball:    "serve receive errors, rotation mistakes, kill shot pressure, team chemistry under stress",
    Tennis:        "double fault pressure, momentum swings, tiebreak nerves, opponent psychology, between-point ritual",
    Swimming:      "start reaction anxiety, flip turn focus, split time obsession, heat assignments, taper mindset",
    "Cross Country":"pacing judgment, hill anxiety, pack running strategy, finishing kick confidence, weather adaptation",
  };
  return map[sport] || "competition pressure, performance anxiety, goal achievement, resilience after setbacks, team dynamics";
}

function getRoutine(sport) {
  const map = {
    Wrestling:  ["Weigh in — trust your prep","Put on your walkout song","3 rounds box breathing","Visualize your first shot","Shadow wrestle 2 min","Say your affirmation out loud","Step on the mat — you own this"],
    Football:   ["Review your key assignments one final time","10 min pump-up music","Dynamic warm-up sequence","5 rounds box breathing","Visualize your first play perfectly","Team huddle — bring your energy","Lock in your one word for today"],
    Basketball: ["Ball-handling warm-up — 10 min","Free throw routine — 20 makes","Visualization: first possession","Box breathing — 3 rounds","Stretch with intention","Team layup lines — full energy","One shooting thought only"],
    Golf:       ["Arrive 45 min early — no rush","Putting green — 15 min","Chip shots — 10 min","4-7-8 breathing — 3 rounds","Visualize your first tee shot","Walk first 3 holes mentally","No phone after warm-up starts","One swing thought only"],
    default:    ["Breathing exercise — 5 min","Visualization — 3 min","Physical warm-up — full effort","Set your intention for today","Affirmation — say it out loud","Lock your focus — compete now"],
  };
  return map[sport] || map.default;
}

function getSystemPrompt(sport, name) {
  return `You are a real human mental performance coach texting with ${name}, a ${sport} athlete. You've worked with athletes for 15 years. You're direct, warm, and real — not a chatbot.

Rules for how you talk:
- Sound like a person texting, not an AI writing an essay
- Use contractions naturally (you're, I've, that's, don't)
- Keep it short. 1-3 sentences most of the time. Never bullet points or numbered lists
- Use ${name}'s name occasionally but not every message
- Sometimes just ask one sharp question back instead of giving advice
- Reference real ${sport} situations: ${getSportContext(sport)}
- Don't summarize what they said back to them
- Never start with "I" — vary your openers
- Occasional filler like "yeah", "look", "honestly", "real talk" is fine
- If they're struggling, don't just validate — push them a little
- End with either a question, a short action, or a direct statement. Not all three.`;
}

/* ─── API helper — proxied through Vite dev server to avoid CORS ─── */
async function claudeChat({ system, messages, max_tokens = 1000 }) {
  const res = await fetch("/api/claude", {
    method: "POST",
    headers: { "Content-Type": "application/json", "x-api-key": import.meta.env.VITE_ANTHROPIC_KEY, "anthropic-version": "2023-06-01" },
    body: JSON.stringify({ model: "claude-sonnet-4-5", max_tokens, system, messages }),
  });
  const data = await res.json();
  if (data.error) throw new Error(data.error.message || JSON.stringify(data.error));
  return data.content?.[0]?.text || "";
}

/* ─────────────────────────── FONTS ─────────────────────────── */
const fontLink = document.createElement("link");
fontLink.rel = "stylesheet";
fontLink.href = "https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Inter:wght@300;400;500;600;700&display=swap";
document.head.appendChild(fontLink);

/* ─────────────────────────── APP ─────────────────────────── */
export default function App() {
  const [screen, setScreen]   = useState("splash");
  const [athlete, setAthlete] = useState({ name:"", sport:"", sportEmoji:"" });
  const [tab, setTab]         = useState("home");
  const [trainModule, setTrainModule] = useState(null);
  const [messages, setMessages]       = useState([]);
  const [chatHistory, setChatHistory] = useState([]);
  const [goals, setGoals]             = useState([]);
  const [journalEntries, setJournalEntries] = useState([]);

  useEffect(() => {
    const t = setTimeout(() => setScreen("onboard"), 1800);
    return () => clearTimeout(t);
  }, []);

  function finishOnboard(name, sport, emoji) {
    setAthlete({ name, sport, sportEmoji: emoji });
    const welcome = `Hey ${name}. Coach here.\n\nI work with ${sport} athletes on the mental side — nerves, slumps, confidence, all of it. That stuff is just as trainable as anything physical.\n\nWhat's going on?`;
    setMessages([{ role:"assistant", content: welcome }]);
    setChatHistory([]);
    setScreen("main");
  }

  if (screen === "splash") return <Splash />;
  if (screen === "onboard") return <Onboard onComplete={finishOnboard} />;

  return (
    <div style={s.app}>
      <div style={s.safeTop} />
      {tab === "home"    && <HomeTab    athlete={athlete} setTab={setTab} setTrainModule={setTrainModule} />}
      {tab === "coach"   && <CoachTab   athlete={athlete} messages={messages} setMessages={setMessages} chatHistory={chatHistory} setChatHistory={setChatHistory} />}
      {tab === "train"   && <TrainHub   athlete={athlete} trainModule={trainModule} setTrainModule={setTrainModule} goals={goals} setGoals={setGoals} />}
      {tab === "journal" && <JournalTab entries={journalEntries} setEntries={setJournalEntries} />}
      {tab === "profile" && <ProfileTab athlete={athlete} goals={goals} journalEntries={journalEntries} />}
      <BottomNav tab={tab} setTab={setTab} />
      <div style={s.safeBottom} />
    </div>
  );
}

/* ─── SPLASH ─── */
function Splash() {
  return (
    <div style={{ ...s.app, ...s.center, background:"#08080c" }}>
      <div style={s.splashMonogram}>
        <span style={s.splashMonogramLetter}>A</span>
      </div>
      <div style={s.splashTitle}>APEX MIND</div>
      <div style={s.splashSub}>MENTAL PERFORMANCE</div>
    </div>
  );
}

/* ─── ONBOARD ─── */
function Onboard({ onComplete }) {
  const [step, setStep]   = useState(0);
  const [name, setName]   = useState("");
  const [sport, setSport] = useState("");
  const [emoji, setEmoji] = useState("");

  function pickSport(sp, e) { setSport(sp); setEmoji(e); }
  function next() {
    if (step === 0 && name.trim()) setStep(1);
    if (step === 1 && sport) onComplete(name.trim(), sport, emoji);
  }

  return (
    <div style={s.onboardWrap}>
      <div style={s.onboardInner}>
        {step === 0 ? (
          <>
            <div style={s.onboardMonogram}>
              <span style={s.onboardMonogramLetter}>A</span>
            </div>
            <h1 style={s.onboardTitle}>APEX MIND</h1>
            <p style={s.onboardSub}>Your personal mental performance coach</p>
            <div style={s.onboardFieldWrap}>
              <label style={s.fieldLabel}>WHAT'S YOUR NAME?</label>
              <input
                style={s.bigInput}
                placeholder="First name"
                value={name}
                onChange={e => setName(e.target.value)}
                onKeyDown={e => e.key === "Enter" && next()}
                autoFocus
              />
            </div>
            <button onClick={next} disabled={!name.trim()} style={{ ...s.primaryBtn, ...(name.trim() ? {} : s.btnDisabled) }}>
              CONTINUE →
            </button>
          </>
        ) : (
          <>
            <h2 style={s.onboardTitle2}>WHAT'S YOUR<br/>SPORT?</h2>
            <p style={s.onboardSub}>Your coach will tailor everything to you.</p>
            <div style={s.sportGrid}>
              {SPORTS.map(sp => (
                <button key={sp.name} onClick={() => pickSport(sp.name, sp.emoji)}
                  style={{ ...s.sportChip, ...(sport === sp.name ? s.sportChipActive : {}) }}>
                  <span style={s.sportEmoji}>{sp.emoji}</span>
                  <span style={s.sportName}>{sp.name}</span>
                </button>
              ))}
            </div>
            <button onClick={next} disabled={!sport} style={{ ...s.primaryBtn, ...(sport ? {} : s.btnDisabled), marginTop:16 }}>
              START TRAINING →
            </button>
          </>
        )}
      </div>
    </div>
  );
}

/* ─── BOTTOM NAV ─── */
function BottomNav({ tab, setTab }) {
  return (
    <div style={s.bottomNav}>
      {TABS.map(t => (
        <button key={t.id} onClick={() => setTab(t.id)} style={{ ...s.navBtn, ...(tab === t.id ? s.navBtnActive : {}) }}>
          <span style={{ ...s.navIcon, ...(tab === t.id ? s.navIconActive : {}) }}>{t.icon}</span>
          <span style={s.navLabel}>{t.label}</span>
          {tab === t.id && <div style={s.navActiveDot} />}
        </button>
      ))}
    </div>
  );
}

/* ─── HOME TAB ─── */
function HomeTab({ athlete, setTab, setTrainModule }) {
  const hour = new Date().getHours();
  const greeting = hour < 12 ? "Good morning" : hour < 17 ? "Good afternoon" : "Good evening";
  const tips = [
    "Confidence is built through action, not thought. Do the work.",
    "The scoreboard reflects your preparation, not your worth.",
    "Champions aren't made on game day. They're revealed.",
    "Your thoughts are not facts. Choose better ones.",
    "Pressure is a privilege. Only those who matter feel it.",
    "Recovery is not weakness. It's strategy.",
  ];
  const tip = tips[new Date().getDay() % tips.length];

  return (
    <div style={s.screen}>
      <div style={s.homeHeader}>
        <div>
          <p style={s.greeting}>{greeting},</p>
          <h1 style={s.heroName}>{athlete.name} {athlete.sportEmoji}</h1>
        </div>
        <div style={s.streakBadge}>
          <span style={s.streakNum}>1</span>
          <span style={s.streakLabel}>DAY</span>
        </div>
      </div>

      <div style={s.heroCard} onClick={() => setTab("coach")}>
        {/* Decorative watermark A */}
        <div style={s.heroCardWatermark}>A</div>
        <div style={s.heroCardLabel}>COACH</div>
        <div style={s.heroCardTitle}>Talk to your{"\n"}coach now</div>
        <div style={s.heroCardArrow}>→</div>
      </div>

      <div style={s.quoteCard}>
        <div style={s.quoteLabel}>TODAY'S MINDSET</div>
        <p style={s.quoteText}>"{tip}"</p>
      </div>

      <p style={s.sectionLabel}>QUICK ACCESS</p>
      <div style={s.quickGrid}>
        {TRAIN_MODULES.map(m => (
          <button key={m.id}
            style={{ ...s.quickCard, borderLeftColor: m.color, borderLeftWidth: 3, borderLeftStyle: "solid" }}
            onClick={() => { setTrainModule(m.id); setTab("train"); }}>
            <span style={{ ...s.quickIcon, color: m.color, fontSize: 22 }}>{m.icon}</span>
            <span style={s.quickLabel}>{m.label}</span>
          </button>
        ))}
      </div>
    </div>
  );
}

/* ─── COACH TAB ─── */
function CoachTab({ athlete, messages, setMessages, chatHistory, setChatHistory }) {
  const [input, setInput]     = useState("");
  const [loading, setLoading] = useState(false);
  const [typingText, setTypingText] = useState("Coach is typing");
  const bottomRef = useRef(null);

  useEffect(() => { bottomRef.current?.scrollIntoView({ behavior:"smooth" }); }, [messages, loading]);

  useEffect(() => {
    if (!loading) return;
    const dots = ["Coach is typing", "Coach is typing.", "Coach is typing..", "Coach is typing..."];
    let i = 0;
    const interval = setInterval(() => { i = (i + 1) % dots.length; setTypingText(dots[i]); }, 500);
    return () => clearInterval(interval);
  }, [loading]);

  const QUICK_PROMPTS = [
    "I'm nervous before my game",
    "I had a bad performance",
    "I'm losing my confidence",
    "Help me stay focused",
  ];

  async function send(text) {
    const msg = text || input.trim();
    if (!msg || loading) return;
    setInput("");
    const userMsg = { role:"user", content: msg };
    const newHistory = [...chatHistory, userMsg];
    setMessages(m => [...m, { role:"user", content: msg }]);
    setChatHistory(newHistory);
    setLoading(true);
    const delay = 800 + Math.random() * 1200;
    await new Promise(r => setTimeout(r, delay));
    try {
      const reply = await claudeChat({ system: getSystemPrompt(athlete.sport, athlete.name), messages: newHistory });
      setMessages(m => [...m, { role:"assistant", content: reply || "Talk to me." }]);
      setChatHistory(h => [...h, { role:"assistant", content: reply || "Talk to me." }]);
    } catch(err) {
      setMessages(m => [...m, { role:"assistant", content:`Error: ${err.message}` }]);
    }
    setLoading(false);
  }

  return (
    <div style={{ ...s.screen, padding:0, display:"flex", flexDirection:"column" }}>
      <div style={s.coachHeader}>
        <div style={s.coachAvatarWrap}>
          <div style={s.coachAvatar}>C</div>
          <div style={s.coachOnlinePip} />
        </div>
        <div>
          <div style={s.coachName}>Coach</div>
          <div style={s.coachSport}>{loading ? <span style={{ color:"#22c55e" }}>{typingText}</span> : `${athlete.sport} · Mental Performance`}</div>
        </div>
      </div>

      <div style={s.messages}>
        {messages.map((m, i) => (
          <div key={i} style={{ ...s.bubbleRow, ...(m.role === "user" ? s.bubbleRowUser : {}) }}>
            {m.role === "assistant" && <div style={s.coachDot}>C</div>}
            <div style={{ ...s.bubble, ...(m.role === "user" ? s.bubbleUser : s.bubbleAI) }}>
              <p style={s.bubbleText}>{m.content}</p>
            </div>
          </div>
        ))}
        {loading && (
          <div style={s.bubbleRow}>
            <div style={s.coachDot}>C</div>
            <div style={{ ...s.bubble, ...s.bubbleAI }}>
              <div style={s.typingDots}><span>•</span><span>•</span><span>•</span></div>
            </div>
          </div>
        )}
        <div ref={bottomRef} />
      </div>

      {messages.length <= 1 && (
        <div style={s.quickPrompts}>
          {QUICK_PROMPTS.map((q, i) => (
            <button key={i} style={s.quickPrompt} onClick={() => send(q)}>{q}</button>
          ))}
        </div>
      )}

      <div style={s.inputBar}>
        <input
          style={s.chatInput}
          value={input}
          onChange={e => setInput(e.target.value)}
          onKeyDown={e => e.key === "Enter" && send()}
          placeholder="Message Coach..."
        />
        <button onClick={() => send()} style={{ ...s.sendBtn, ...((!input.trim() || loading) ? s.sendBtnDisabled : {}) }}>↑</button>
      </div>
    </div>
  );
}

/* ─── TRAIN HUB ─── */
function TrainHub({ athlete, trainModule, setTrainModule, goals, setGoals }) {
  if (trainModule === "breathe")   return <BreatheModule   onBack={() => setTrainModule(null)} />;
  if (trainModule === "meditate")  return <MeditateModule  onBack={() => setTrainModule(null)} />;
  if (trainModule === "visualize") return <VisualizeModule onBack={() => setTrainModule(null)} sport={athlete.sport} />;
  if (trainModule === "routine")   return <RoutineModule   onBack={() => setTrainModule(null)} sport={athlete.sport} />;
  if (trainModule === "goals")     return <GoalsModule     onBack={() => setTrainModule(null)} goals={goals} setGoals={setGoals} sport={athlete.sport} />;
  if (trainModule === "recovery")  return <RecoveryModule  onBack={() => setTrainModule(null)} sport={athlete.sport} name={athlete.name} />;

  return (
    <div style={s.screen}>
      <h1 style={s.pageTitle}>MENTAL TRAINING</h1>
      <p style={s.pageSub}>Build your mental game one session at a time.</p>
      <div style={s.moduleGrid}>
        {TRAIN_MODULES.map(m => (
          <button key={m.id} onClick={() => setTrainModule(m.id)}
            style={{ ...s.moduleCard, borderLeft:`3px solid ${m.color}` }}>
            <div style={{ fontSize:24, color: m.color, marginBottom:12 }}>{m.icon}</div>
            <div style={s.moduleLabel}>{m.label}</div>
            <div style={s.moduleDesc}>{m.desc}</div>
            <div style={{ ...s.moduleArrow, color: m.color }}>→</div>
          </button>
        ))}
      </div>
    </div>
  );
}

/* ─── BREATHE MODULE ─── */
function BreatheModule({ onBack }) {
  const [sel, setSel]     = useState(0);
  const [phase, setPhase] = useState(null);
  const [count, setCount] = useState(0);
  const [rounds, setRounds] = useState(0);
  const [scale, setScale] = useState(1);
  const timerRef = useRef(null);
  const pat = BREATH_PATTERNS[sel];

  function stop() { clearTimeout(timerRef.current); setPhase(null); setCount(0); setScale(1); }

  function start() { stop(); setRounds(0); runPhase("inhale", pat.inhale); }

  function runPhase(p, secs) {
    setPhase(p);
    setScale(p === "inhale" ? 1.4 : p === "exhale" ? 0.8 : 1.15);
    let s = secs; setCount(s);
    const tick = () => {
      s--;
      if (s <= 0) {
        const nx = nextPhase(p);
        if (nx) runPhase(nx.p, nx.s);
        else { setRounds(r => r + 1); runPhase("inhale", pat.inhale); }
      } else { setCount(s); timerRef.current = setTimeout(tick, 1000); }
    };
    timerRef.current = setTimeout(tick, 1000);
  }

  function nextPhase(p) {
    if (p === "inhale"  && pat.hold1 > 0) return { p:"hold1",  s:pat.hold1  };
    if (p === "inhale"  && pat.hold1 <= 0) return { p:"exhale", s:pat.exhale };
    if (p === "hold1")                     return { p:"exhale", s:pat.exhale };
    if (p === "exhale"  && pat.hold2 > 0) return { p:"hold2",  s:pat.hold2  };
    return null;
  }

  useEffect(() => () => clearTimeout(timerRef.current), []);

  const LABELS = { inhale:"INHALE", hold1:"HOLD", exhale:"EXHALE", hold2:"HOLD" };

  return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={onBack}>← Back</button>
      <h1 style={s.pageTitle}>BREATHE</h1>
      <div style={s.patternRow}>
        {BREATH_PATTERNS.map((p, i) => (
          <button key={i} onClick={() => { stop(); setSel(i); }}
            style={{ ...s.patternBtn, ...(sel === i ? { borderColor: p.color, color: p.color, background: p.color + "12" } : {}) }}>
            <span style={s.patternTag}>{p.tag}</span>
            <span style={s.patternName}>{p.name}</span>
          </button>
        ))}
      </div>
      <div style={s.breathCenter}>
        <div style={{ ...s.breathRing, transform:`scale(${scale})`, borderColor: pat.color, boxShadow:`0 0 60px ${pat.color}55`, transition:"transform 1s ease, box-shadow 1s ease" }}>
          <div style={s.breathInner}>
            {phase ? (
              <>
                <div style={{ ...s.breathPhaseLabel, color: pat.color }}>{LABELS[phase]}</div>
                <div style={{ ...s.breathCountNum, color: pat.color }}>{count}</div>
              </>
            ) : <span style={{ color:"#5a5a72", fontSize:13, letterSpacing:2 }}>READY</span>}
          </div>
        </div>
        {rounds > 0 && <p style={s.roundsText}>{rounds} round{rounds > 1 ? "s" : ""} complete</p>}
      </div>
      <p style={s.breathDesc}>{pat.desc}</p>
      {!phase
        ? <button onClick={start} style={s.primaryBtn}>START SESSION</button>
        : <button onClick={stop}  style={{ ...s.primaryBtn, background:"#18181f", color:"#5a5a72" }}>STOP</button>
      }
    </div>
  );
}

/* ─── MEDITATE MODULE ─── */
function MeditateModule({ onBack }) {
  const [active, setActive]   = useState(null);
  const [lineIdx, setLineIdx] = useState(0);
  const [elapsed, setElapsed] = useState(0);
  const timerRef = useRef(null);

  function startMed(med) {
    setActive(med); setLineIdx(0); setElapsed(0);
    const secPerLine = Math.floor(med.duration / med.script.length);
    let e = 0, l = 0;
    timerRef.current = setInterval(() => {
      e++; setElapsed(e);
      if (e > 0 && e % secPerLine === 0 && l < med.script.length - 1) { l++; setLineIdx(l); }
      if (e >= med.duration) { clearInterval(timerRef.current); }
    }, 1000);
  }

  function stopMed() { clearInterval(timerRef.current); setActive(null); }
  useEffect(() => () => clearInterval(timerRef.current), []);

  if (active) {
    const prog   = Math.min(elapsed / active.duration, 1);
    const minSec = `${Math.floor(elapsed/60)}:${String(elapsed%60).padStart(2,"0")}`;
    const totSec = `${Math.floor(active.duration/60)}:00`;
    return (
      <div style={{ ...s.screen, ...s.meditateActive }}>
        <div style={{ ...s.meditateOrb, background:`radial-gradient(circle, ${active.color}44 0%, transparent 70%)` }} />
        <div style={s.meditateIconBig}>{active.icon}</div>
        <h2 style={s.meditateTitleActive}>{active.title}</h2>
        <div style={s.progBarWrap}><div style={{ ...s.progFill, width:`${prog*100}%`, background: active.color }} /></div>
        <p style={s.meditateTime}>{minSec} / {totSec}</p>
        <p style={s.meditateScript}>{active.script[lineIdx]}</p>
        <button onClick={stopMed} style={{ ...s.primaryBtn, background:"#18181f", color:"#5a5a72", marginTop:32 }}>END SESSION</button>
      </div>
    );
  }

  return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={onBack}>← Back</button>
      <h1 style={s.pageTitle}>MEDITATE</h1>
      <p style={s.pageSub}>Guided mental sessions for every situation.</p>
      {MEDITATIONS.map(m => (
        <button key={m.id} onClick={() => startMed(m)} style={s.medCard}>
          <div style={{ ...s.medIconWrap, background: m.color + "18" }}>
            <span style={{ fontSize:22, color: m.color }}>{m.icon}</span>
          </div>
          <div style={s.medInfo}>
            <div style={s.medTitle}>{m.title}</div>
            <div style={s.medMeta}>
              <span style={{ ...s.medTag, background: m.color + "22", color: m.color }}>{m.tag}</span> · {Math.floor(m.duration/60)} min
            </div>
          </div>
          <span style={{ color:"#2e2e3e", fontSize:18 }}>›</span>
        </button>
      ))}
    </div>
  );
}

/* ─── VISUALIZE MODULE ─── */
function VisualizeModule({ onBack, sport }) {
  const [active, setActive] = useState(null);
  const [text, setText]     = useState("");
  const [loading, setLoading] = useState(false);

  async function run(v) {
    setActive(v); setText(""); setLoading(true);
    try {
      const prompt = v.prompt.replace(/{sport}/g, sport);
      const result = await claudeChat({
        system: `You are a sports visualization coach. Write an immersive guided visualization for a ${sport} athlete. Second person, present tense, sensory-rich. No bullet points. No headers.`,
        messages: [{ role:"user", content: prompt }],
      });
      setText(result);
    } catch { setText("Unable to load. Check connection and try again."); }
    setLoading(false);
  }

  if (active) return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={() => setActive(null)}>← Back</button>
      <div style={{ textAlign:"center", margin:"8px 0 24px" }}>
        <div style={{ fontSize:36, color: active.color }}>{active.icon}</div>
        <h2 style={s.pageTitle}>{active.title.toUpperCase()}</h2>
      </div>
      {loading
        ? <div style={s.loadingState}><div style={s.loadingDot} />Generating your visualization...</div>
        : <div style={s.scriptCard}>{text}</div>
      }
    </div>
  );

  return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={onBack}>← Back</button>
      <h1 style={s.pageTitle}>VISUALIZE</h1>
      <p style={s.pageSub}>Mental rehearsal is training. Your brain treats it as real.</p>
      {VISUALIZATIONS.map(v => (
        <button key={v.id} onClick={() => run(v)} style={s.medCard}>
          <div style={{ ...s.medIconWrap, background: v.color + "18" }}>
            <span style={{ fontSize:22, color: v.color }}>{v.icon}</span>
          </div>
          <div style={s.medInfo}>
            <div style={s.medTitle}>{v.title}</div>
            <div style={{ ...s.medTag, background: v.color + "22", color: v.color, display:"inline-block", marginTop:4 }}>AI-GENERATED FOR {sport.toUpperCase()}</div>
          </div>
          <span style={{ color:"#2e2e3e", fontSize:18 }}>›</span>
        </button>
      ))}
    </div>
  );
}

/* ─── ROUTINE MODULE ─── */
function RoutineModule({ onBack, sport }) {
  const list = getRoutine(sport);
  const [checked, setChecked] = useState({});
  const done = Object.values(checked).filter(Boolean).length;
  const all  = done === list.length;

  return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={onBack}>← Back</button>
      <h1 style={s.pageTitle}>PRE-GAME</h1>
      <p style={s.pageSub}>{sport} mental activation sequence.</p>
      <div style={s.progBarWrap}>
        <div style={{ ...s.progFill, width:`${(done/list.length)*100}%`, background:"#7c3aed", transition:"width 0.4s ease" }} />
      </div>
      <p style={{ color:"#5a5a72", fontSize:12, letterSpacing:1, marginBottom:20 }}>{done} / {list.length} COMPLETE</p>
      {list.map((item, i) => (
        <div key={i} onClick={() => setChecked(c => ({ ...c, [i]:!c[i] }))}
          style={{ ...s.routineItem, ...(checked[i] ? s.routineItemDone : {}) }}>
          <div style={{ ...s.routineCheck, ...(checked[i] ? s.routineCheckDone : {}) }}>{checked[i] && "✓"}</div>
          <span style={{ flex:1, opacity: checked[i] ? 0.35 : 1 }}>{item}</span>
        </div>
      ))}
      {all && (
        <div style={s.lockedIn}>
          <span style={{ fontSize:24, color:"#22c55e" }}>✓</span>
          <span style={s.lockedInText}>YOU'RE LOCKED IN</span>
        </div>
      )}
    </div>
  );
}

/* ─── GOALS MODULE ─── */
const GOAL_PROMPTS = {
  Wrestling:     ["Make varsity this season","Win a tournament","Improve my mental toughness on the mat","Lose zero points in the first period","Handle pressure in overtime"],
  Football:      ["Start every game this season","Make zero mental errors on assignments","Stay locked in after a bad play","Build my pre-game routine","Lead the team in my position"],
  Basketball:    ["Shoot 80% from the free throw line","Never let a miss affect my next shot","Become the most composed player on the team","Build a consistent pre-game ritual","Lead in clutch moments"],
  Baseball:      ["Get out of slumps faster","Develop an unshakeable walk-up routine","Stay mentally tough through a long season","Improve focus during at-bats","Be confident against any pitcher"],
  Soccer:        ["Score in high pressure moments","Never let a miss shake my confidence","Stay focused for 90 minutes","Convert penalty kicks","Control my emotions after mistakes"],
  Golf:          ["Break 80 consistently","Never let one bad hole affect the next","Build a rock-solid pre-shot routine","Stay composed on the back nine","Win a tournament"],
  "Track & Field":["Hit a new PR this season","Control my nerves at the start line","Pace myself perfectly in every race","Bounce back from a bad heat","Qualify for state"],
  Volleyball:    ["Serve under pressure with confidence","Let errors go immediately","Be the most locked-in player on the court","Improve mental focus during rallies","Lead by example in tough moments"],
  Tennis:        ["Win more tiebreaks","Eliminate double faults under pressure","Build a between-point reset ritual","Stay composed during momentum swings","Beat players ranked above me"],
  Swimming:      ["Drop time at every meet","Master my mental taper","Perfect my start reaction","Stay calm in the ready room","Qualify for the next level"],
  "Cross Country":["Run smart and confident","Push through the hard miles mentally","Trust my training on race day","Build a race-day routine","Qualify for state"],
  Gymnastics:    ["Stick my landings under pressure","Perform my best when it counts most","Build mental consistency","Compete without fear","Hit every routine in competition"],
  Lacrosse:      ["Perform in clutch moments","Stay focused for full 60 minutes","Build leadership presence","Make smart decisions under pressure","Contribute to team culture"],
  Hockey:        ["Stay sharp every shift","Bounce back after mistakes quickly","Build a pre-game mental routine","Perform in overtime","Lead by example"],
  Other:         ["Compete with full confidence","Build mental toughness","Perform under pressure","Develop a pre-competition routine","Bounce back from setbacks faster"],
};

function getGoalPrompts(sport) {
  return GOAL_PROMPTS[sport] || GOAL_PROMPTS.Other;
}

function GoalsModule({ onBack, goals, setGoals, sport }) {
  const [input, setInput] = useState("");
  const [filter, setFilter] = useState("all");
  const prompts = getGoalPrompts(sport);

  function add(text) {
    const t = text || input.trim();
    if (!t) return;
    setGoals(g => [...g, { id:Date.now(), text:t, done:false, date:new Date().toLocaleDateString() }]);
    setInput("");
  }

  const filtered = filter === "all" ? goals : filter === "active" ? goals.filter(g => !g.done) : goals.filter(g => g.done);
  const alreadyAdded = new Set(goals.map(g => g.text));

  return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={onBack}>← Back</button>
      <h1 style={s.pageTitle}>GOALS</h1>
      <p style={s.pageSub}>Set targets. Track progress. Raise your ceiling.</p>

      <div style={s.inputRow}>
        <input style={s.lineInput} value={input} onChange={e=>setInput(e.target.value)}
          onKeyDown={e=>e.key==="Enter"&&add()} placeholder={`Your ${sport} goal...`} />
        <button onClick={() => add()} style={s.addBtn}>+</button>
      </div>

      <div style={s.goalPromptsWrap}>
        <p style={s.goalPromptsLabel}>SUGGESTED FOR {sport.toUpperCase()}</p>
        <div style={s.goalPromptsList}>
          {prompts.map((p, i) => {
            const added = alreadyAdded.has(p);
            return (
              <button key={i} onClick={() => !added && add(p)}
                style={{ ...s.goalPromptChip, ...(added ? s.goalPromptChipAdded : {}) }}>
                {added ? "✓ " : "+ "}{p}
              </button>
            );
          })}
        </div>
      </div>

      <div style={s.filterRow}>
        {["all","active","done"].map(f => (
          <button key={f} onClick={() => setFilter(f)}
            style={{ ...s.filterBtn, ...(filter===f ? s.filterBtnActive : {}) }}>
            {f.toUpperCase()}
          </button>
        ))}
      </div>

      {filtered.length === 0 && <p style={s.emptyState}>No goals here yet.</p>}
      {filtered.map(g => (
        <div key={g.id} style={s.goalRow}>
          <div onClick={() => setGoals(gs => gs.map(x => x.id===g.id ? {...x,done:!x.done} : x))}
            style={{ ...s.goalCheck, ...(g.done ? s.goalCheckDone : {}) }}>
            {g.done && "✓"}
          </div>
          <div style={{ flex:1 }}>
            <p style={{ ...s.goalText, ...(g.done ? { textDecoration:"line-through", opacity:.35 } : {}) }}>{g.text}</p>
            <p style={s.goalDate}>{g.date}</p>
          </div>
          <button onClick={() => setGoals(gs => gs.filter(x=>x.id!==g.id))} style={s.deleteBtn}>✕</button>
        </div>
      ))}
    </div>
  );
}

/* ─── RECOVERY MODULE ─── */
function RecoveryModule({ onBack, sport, name }) {
  const [input, setInput]       = useState("");
  const [response, setResponse] = useState("");
  const [loading, setLoading]   = useState(false);

  async function getSupport() {
    if (!input.trim() || loading) return;
    setLoading(true); setResponse("");
    try {
      const result = await claudeChat({
        system: `You are a compassionate but direct mental performance coach for ${sport} athletes. ${name} is sharing a setback. Respond in three parts: 1) Acknowledge and validate (2 sentences), 2) Reframe constructively — what this setback really means (2-3 sentences), 3) Two specific, actionable next steps tailored to ${sport}. Be real and specific, not generic.`,
        messages: [{ role:"user", content: input }],
      });
      setResponse(result);
    } catch { setResponse("Connection issue. Try again."); }
    setLoading(false);
  }

  return (
    <div style={s.screen}>
      <button style={s.backBtn} onClick={onBack}>← Back</button>
      <h1 style={s.pageTitle}>RECOVERY</h1>
      <p style={s.pageSub}>Every champion has been here. What matters is what happens next.</p>
      <textarea style={s.textarea} value={input} onChange={e=>setInput(e.target.value)}
        placeholder={`What happened? Tell your coach... (e.g. "I lost my match in overtime", "I threw 3 interceptions", "I keep choking on the back nine")`}
        rows={4} />
      <button onClick={getSupport} disabled={!input.trim() || loading}
        style={{ ...s.primaryBtn, ...((!input.trim()||loading)?s.btnDisabled:{}) }}>
        {loading ? "PROCESSING..." : "GET SUPPORT"}
      </button>
      {response && <div style={s.scriptCard}>{response}</div>}
    </div>
  );
}

/* ─── JOURNAL TAB ─── */
function JournalTab({ entries, setEntries }) {
  const [writing, setWriting] = useState(false);
  const [text, setText]       = useState("");
  const [mood, setMood]       = useState(3);
  const [energy, setEnergy]   = useState(3);
  const MOODS  = ["😤","😞","😐","😊","🔥"];
  const ENERGY = ["💀","😓","⚡","💪","🚀"];

  function save() {
    if (!text.trim()) return;
    setEntries(e => [{ id:Date.now(), text:text.trim(), mood, energy, date:new Date().toLocaleString() }, ...e]);
    setText(""); setMood(3); setEnergy(3); setWriting(false);
  }

  return (
    <div style={s.screen}>
      <div style={s.journalHeader}>
        <h1 style={s.pageTitle}>JOURNAL</h1>
        {!writing && <button onClick={() => setWriting(true)} style={s.newEntryBtn}>+ NEW</button>}
      </div>
      {writing && (
        <div style={s.entryForm}>
          <div style={s.emojiRow}>
            <div>
              <p style={s.emojiLabel}>MOOD</p>
              <div style={s.emojiGroup}>{MOODS.map((e,i) => <button key={i} onClick={() => setMood(i)} style={{ ...s.emojiBtn, ...(mood===i?s.emojiBtnActive:{}) }}>{e}</button>)}</div>
            </div>
            <div>
              <p style={s.emojiLabel}>ENERGY</p>
              <div style={s.emojiGroup}>{ENERGY.map((e,i) => <button key={i} onClick={() => setEnergy(i)} style={{ ...s.emojiBtn, ...(energy===i?s.emojiBtnActive:{}) }}>{e}</button>)}</div>
            </div>
          </div>
          <textarea style={s.textarea} value={text} onChange={e=>setText(e.target.value)}
            placeholder="How was training or competition today? What worked? What will you improve?" rows={5} />
          <div style={{ display:"flex", gap:8 }}>
            <button onClick={save} style={{ ...s.primaryBtn, flex:1 }}>SAVE ENTRY</button>
            <button onClick={() => setWriting(false)} style={{ ...s.primaryBtn, flex:0.4, background:"#1a1a1a", color:"#666" }}>CANCEL</button>
          </div>
        </div>
      )}
      {entries.length === 0 && !writing && <p style={s.emptyState}>No entries yet. Log your first session.</p>}
      {entries.map(e => (
        <div key={e.id} style={s.journalCard}>
          <div style={s.journalCardHeader}>
            <span style={{ fontSize:20 }}>{MOODS[e.mood]}{ENERGY[e.energy]}</span>
            <span style={s.journalDate}>{e.date}</span>
          </div>
          <p style={s.journalText}>{e.text}</p>
        </div>
      ))}
    </div>
  );
}

/* ─── PROFILE TAB ─── */
function ProfileTab({ athlete, goals, journalEntries }) {
  const done = goals.filter(g => g.done).length;
  return (
    <div style={s.screen}>
      <div style={s.profileHero}>
        <div style={s.profileAvatar}>{athlete.sportEmoji}</div>
        <h2 style={s.profileName}>{athlete.name}</h2>
        <p style={s.profileSport}>{athlete.sport} Athlete</p>
      </div>
      <div style={s.statsRow}>
        <div style={s.statCard}><div style={s.statNum}>{journalEntries.length}</div><div style={s.statLabel}>SESSIONS</div></div>
        <div style={s.statCard}><div style={s.statNum}>{goals.length}</div><div style={s.statLabel}>GOALS SET</div></div>
        <div style={s.statCard}><div style={s.statNum}>{done}</div><div style={s.statLabel}>ACHIEVED</div></div>
      </div>
      <div style={s.aboutCard}>
        <p style={s.aboutTitle}>ABOUT THIS APP</p>
        <p style={s.aboutText}>Apex Mind is a mental performance coaching app powered by AI. Every feature is tailored to your sport and designed to help you train your mind with the same intention you bring to your physical training.</p>
        <p style={s.aboutText}>Built with the same psychology frameworks used by Olympic and professional athletes.</p>
      </div>
    </div>
  );
}

/* ─────────────────────────── STYLES ─────────────────────────── */
const C = {
  bg:        "#060608",
  surface:   "#0e0e14",
  surfaceHi: "#14141c",
  border:    "#1e1e28",
  borderHi:  "#2a2a38",
  text:      "#f0f0f6",
  sub:       "#64647a",
  accent:    "#ef4444",
  accentGlow:"#ef444433",
  green:     "#10b981",
  purple:    "#8b5cf6",
  cyan:      "#06b6d4",
  amber:     "#f59e0b",
};

const s = {
  app:        { background:C.bg, minHeight:"100dvh", color:C.text, fontFamily:"'Outfit', sans-serif", display:"flex", flexDirection:"column", maxWidth:480, margin:"0 auto", position:"relative" },
  safeTop:    { height:"env(safe-area-inset-top, 12px)", background:C.bg },
  safeBottom: { height:"env(safe-area-inset-bottom, 8px)", background:C.bg },
  center:     { display:"flex", flexDirection:"column", alignItems:"center", justifyContent:"center" },
  splashLogo:  { fontSize:72, marginBottom:16, filter:"drop-shadow(0 0 32px #ef444488)" },
  splashTitle: { fontFamily:"'Bebas Neue', sans-serif", fontSize:56, letterSpacing:14, color:C.text, textShadow:"0 0 40px #ef444466" },
  splashSub:   { fontSize:11, letterSpacing:6, color:C.sub, marginTop:6 },
  onboardWrap:  { minHeight:"100dvh", background:`radial-gradient(ellipse at top, #120810 0%, ${C.bg} 60%)`, display:"flex", alignItems:"center", justifyContent:"center", padding:"24px 20px" },
  onboardInner: { width:"100%", maxWidth:420 },
  onboardIcon:  { fontSize:56, textAlign:"center", display:"block", marginBottom:14, filter:"drop-shadow(0 0 20px #ef444466)" },
  onboardTitle: { fontFamily:"'Bebas Neue', sans-serif", fontSize:54, letterSpacing:8, textAlign:"center", margin:"0 0 6px" },
  onboardTitle2:{ fontFamily:"'Bebas Neue', sans-serif", fontSize:46, letterSpacing:4, textAlign:"center", margin:"0 0 6px", lineHeight:1.1 },
  onboardSub:   { color:C.sub, textAlign:"center", marginBottom:32, fontSize:14 },
  onboardFieldWrap: { marginBottom:24 },
  fieldLabel:   { display:"block", fontSize:10, letterSpacing:3, color:C.sub, marginBottom:8 },
  bigInput:     { width:"100%", background:C.surface, border:`1px solid ${C.border}`, borderRadius:16, padding:"16px 18px", color:C.text, fontSize:18, fontFamily:"'Outfit', sans-serif", boxSizing:"border-box", outline:"none" },
  sportGrid:    { display:"flex", flexWrap:"wrap", gap:8, marginBottom:8 },
  sportChip:    { display:"flex", alignItems:"center", gap:6, background:C.surface, border:`1px solid ${C.border}`, borderRadius:50, padding:"9px 15px", color:C.sub, cursor:"pointer", fontSize:13, fontFamily:"'Outfit', sans-serif", transition:"all 0.15s" },
  sportChipActive: { background:"#1e1028", border:`1px solid ${C.accent}88`, color:C.text, boxShadow:`0 0 12px ${C.accentGlow}` },
  sportEmoji:   { fontSize:16 },
  sportName:    { fontSize:12, fontWeight:500 },
  primaryBtn:   { width:"100%", background:`linear-gradient(135deg, #ef4444, #cc2222)`, color:"#fff", border:"none", borderRadius:14, padding:"16px", fontSize:13, letterSpacing:3, fontWeight:700, cursor:"pointer", fontFamily:"'Outfit', sans-serif", transition:"opacity 0.15s", boxShadow:`0 4px 20px ${C.accentGlow}` },
  btnDisabled:  { opacity:0.25, cursor:"not-allowed", boxShadow:"none" },
  bottomNav:  { display:"flex", borderTop:`1px solid ${C.border}`, background:`${C.bg}ee`, backdropFilter:"blur(12px)", padding:"8px 0 2px", flexShrink:0, position:"sticky", bottom:0 },
  navBtn:     { flex:1, display:"flex", flexDirection:"column", alignItems:"center", gap:3, background:"none", border:"none", color:C.sub, cursor:"pointer", padding:"4px 0" },
  navBtnActive:{ color:C.text },
  navIcon:    { fontSize:20 },
  navLabel:   { fontSize:9, letterSpacing:1, fontFamily:"'Outfit', sans-serif", fontWeight:500 },
  screen:     { flex:1, overflowY:"auto", padding:"16px 16px 24px", paddingBottom:88 },
  homeHeader: { display:"flex", justifyContent:"space-between", alignItems:"flex-start", marginBottom:22 },
  greeting:   { fontSize:13, color:C.sub, margin:"0 0 2px", letterSpacing:0.5 },
  heroName:   { fontFamily:"'Bebas Neue', sans-serif", fontSize:44, letterSpacing:3, margin:0, lineHeight:1 },
  streakBadge:{ background:C.surface, border:`1px solid ${C.border}`, borderRadius:14, padding:"10px 14px", textAlign:"center" },
  streakNum:  { display:"block", fontSize:24, fontWeight:700, lineHeight:1, color:C.amber },
  streakLabel:{ display:"block", fontSize:9, letterSpacing:2, color:C.sub, marginTop:2 },
  heroCard:   { background:`linear-gradient(135deg, #1e0a0a 0%, #2a0c0c 50%, #1a0808 100%)`, border:`1px solid ${C.accent}44`, borderRadius:22, padding:"24px 22px", marginBottom:14, cursor:"pointer", position:"relative", overflow:"hidden", boxShadow:`0 8px 32px ${C.accentGlow}` },
  heroCardLabel:{ fontSize:10, letterSpacing:3, color:C.accent, marginBottom:8, fontWeight:700 },
  heroCardTitle:{ fontFamily:"'Bebas Neue', sans-serif", fontSize:38, letterSpacing:2, lineHeight:1.1, whiteSpace:"pre-line" },
  heroCardArrow:{ position:"absolute", right:22, top:"50%", transform:"translateY(-50%)", fontSize:30, color:C.accent, opacity:0.8 },
  quoteCard:  { background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:18, padding:"18px 20px", marginBottom:22 },
  quoteLabel: { fontSize:9, letterSpacing:3, color:C.sub, marginBottom:10, fontWeight:700 },
  quoteText:  { fontSize:14, color:"#b8b8cc", lineHeight:1.7, margin:0, fontStyle:"italic" },
  sectionLabel:{ fontSize:10, letterSpacing:3, color:C.sub, marginBottom:14, fontWeight:700 },
  quickGrid:  { display:"grid", gridTemplateColumns:"1fr 1fr 1fr", gap:10 },
  quickCard:  { background:C.surface, border:`1px solid`, borderRadius:16, padding:"16px 10px", display:"flex", flexDirection:"column", alignItems:"center", gap:8, cursor:"pointer", fontFamily:"'Outfit', sans-serif" },
  quickIcon:  { width:42, height:42, borderRadius:12, display:"flex", alignItems:"center", justifyContent:"center", fontSize:20 },
  quickLabel: { fontSize:11, fontWeight:600, letterSpacing:0.5, color:C.text },
  coachHeader:{ display:"flex", alignItems:"center", gap:12, padding:"14px 16px", borderBottom:`1px solid ${C.border}`, background:`${C.bg}f0`, backdropFilter:"blur(12px)" },
  coachAvatarWrap:{ position:"relative", flexShrink:0 },
  coachAvatar:{ width:44, height:44, borderRadius:50, background:`linear-gradient(135deg, #1e1030, #2a1844)`, border:`1px solid ${C.purple}44`, display:"flex", alignItems:"center", justifyContent:"center", fontSize:17, fontWeight:700, color:"#fff", fontFamily:"'Bebas Neue', sans-serif", letterSpacing:1, boxShadow:`0 0 16px ${C.purple}22` },
  coachOnlinePip:{ position:"absolute", bottom:1, right:1, width:11, height:11, borderRadius:50, background:C.green, border:`2px solid ${C.bg}`, boxShadow:`0 0 8px ${C.green}88` },
  coachName:  { fontSize:15, fontWeight:700 },
  coachSport: { fontSize:12, color:C.sub },
  onlineDot:  { width:8, height:8, borderRadius:50, background:C.green, marginLeft:"auto", marginRight:4, boxShadow:`0 0 8px ${C.green}88` },
  messages:   { flex:1, overflowY:"auto", padding:"16px", display:"flex", flexDirection:"column", gap:14 },
  bubbleRow:  { display:"flex", gap:8, alignItems:"flex-end" },
  bubbleRowUser:{ flexDirection:"row-reverse" },
  coachDot:   { width:30, height:30, borderRadius:50, background:`linear-gradient(135deg, #1e1030, #2a1844)`, border:`1px solid ${C.purple}44`, display:"flex", alignItems:"center", justifyContent:"center", fontSize:12, fontWeight:700, color:"#c4b5fd", fontFamily:"'Bebas Neue', sans-serif", flexShrink:0 },
  bubble:     { maxWidth:"80%", borderRadius:20, padding:"12px 16px" },
  bubbleAI:   { background:C.surfaceHi, borderBottomLeftRadius:4, border:`1px solid ${C.border}` },
  bubbleUser: { background:`linear-gradient(135deg, #1c1c30, #24243c)`, borderBottomRightRadius:4 },
  bubbleText: { margin:0, fontSize:14, lineHeight:1.7, color:"#ddddf0" },
  typingDots: { display:"flex", gap:5, alignItems:"center", height:18, color:C.sub, fontSize:20 },
  quickPrompts:{ display:"flex", flexWrap:"wrap", gap:8, padding:"0 16px 12px" },
  quickPrompt:{ background:C.surfaceHi, border:`1px solid ${C.border}`, borderRadius:50, padding:"9px 16px", color:"#9898b8", fontSize:12, cursor:"pointer", fontFamily:"'Outfit', sans-serif" },
  inputBar:   { display:"flex", gap:8, padding:"10px 14px 12px", borderTop:`1px solid ${C.border}`, background:`${C.bg}f0`, backdropFilter:"blur(12px)" },
  chatInput:  { flex:1, background:C.surfaceHi, border:`1px solid ${C.border}`, borderRadius:50, padding:"13px 20px", color:C.text, fontSize:14, fontFamily:"'Outfit', sans-serif", outline:"none" },
  sendBtn:    { width:46, height:46, borderRadius:50, background:`linear-gradient(135deg, #ef4444, #cc2222)`, color:"#fff", border:"none", fontSize:18, cursor:"pointer", fontWeight:700, flexShrink:0, display:"flex", alignItems:"center", justifyContent:"center", boxShadow:`0 4px 16px ${C.accentGlow}` },
  sendBtnDisabled:{ opacity:0.3, cursor:"not-allowed", boxShadow:"none" },
  pageTitle:  { fontFamily:"'Bebas Neue', sans-serif", fontSize:40, letterSpacing:4, margin:"0 0 4px" },
  pageSub:    { color:C.sub, fontSize:13, marginBottom:24, lineHeight:1.5 },
  moduleGrid: { display:"grid", gridTemplateColumns:"1fr 1fr", gap:12 },
  moduleCard: { background:`linear-gradient(145deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:20, padding:"20px 16px", cursor:"pointer", textAlign:"left", fontFamily:"'Outfit', sans-serif", position:"relative" },
  moduleIconWrap:{ width:54, height:54, borderRadius:16, border:`1px solid`, display:"flex", alignItems:"center", justifyContent:"center", marginBottom:14 },
  moduleLabel:{ fontSize:15, fontWeight:700, color:C.text, marginBottom:4 },
  moduleDesc: { fontSize:12, color:C.sub, lineHeight:1.4 },
  moduleArrow:{ position:"absolute", top:18, right:16, fontSize:16, opacity:0.6 },
  patternRow: { display:"flex", gap:8, marginBottom:28 },
  patternBtn: { flex:1, background:C.surface, border:`1px solid ${C.border}`, borderRadius:14, padding:"12px 8px", cursor:"pointer", fontFamily:"'Outfit', sans-serif", color:C.sub, textAlign:"center" },
  patternTag: { display:"block", fontSize:9, letterSpacing:2, fontWeight:700, marginBottom:4 },
  patternName:{ display:"block", fontSize:11, fontWeight:600 },
  breathCenter:{ display:"flex", flexDirection:"column", alignItems:"center", margin:"8px 0 28px" },
  breathRing: { width:190, height:190, borderRadius:"50%", border:"2px solid", display:"flex", alignItems:"center", justifyContent:"center" },
  breathInner:{ textAlign:"center" },
  breathPhaseLabel:{ fontSize:10, letterSpacing:4, fontWeight:700, marginBottom:6 },
  breathCountNum:  { fontSize:58, fontWeight:700, lineHeight:1, fontFamily:"'Bebas Neue', sans-serif" },
  breathDesc: { textAlign:"center", color:C.sub, fontSize:13, marginBottom:22, lineHeight:1.5 },
  roundsText: { color:C.sub, fontSize:12, letterSpacing:1, marginTop:14 },
  meditateActive:{ display:"flex", flexDirection:"column", alignItems:"center", padding:"24px 20px 80px", textAlign:"center", minHeight:"100dvh", position:"relative", overflow:"hidden", background:`radial-gradient(ellipse at top, #100820 0%, ${C.bg} 60%)` },
  meditateOrb:   { position:"absolute", top:0, left:0, right:0, height:320, pointerEvents:"none", zIndex:0 },
  meditateIconBig:{ fontSize:60, marginBottom:14, position:"relative", zIndex:1 },
  meditateTitleActive:{ fontFamily:"'Bebas Neue', sans-serif", fontSize:32, letterSpacing:4, margin:"0 0 22px", position:"relative", zIndex:1 },
  meditateTime:  { color:C.sub, fontSize:12, letterSpacing:2, margin:"8px 0 30px", position:"relative", zIndex:1 },
  meditateScript:{ fontSize:18, lineHeight:1.9, color:"#c0c0dc", maxWidth:340, position:"relative", zIndex:1, minHeight:100 },
  medCard:    { display:"flex", alignItems:"center", gap:14, background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:18, padding:"18px", marginBottom:10, cursor:"pointer", fontFamily:"'Outfit', sans-serif", width:"100%", textAlign:"left" },
  medIconWrap:{ width:50, height:50, borderRadius:14, display:"flex", alignItems:"center", justifyContent:"center", flexShrink:0 },
  medInfo:    { flex:1 },
  medTitle:   { fontSize:15, fontWeight:600, color:C.text, marginBottom:5 },
  medMeta:    { fontSize:12, color:C.sub, display:"flex", alignItems:"center", gap:6 },
  medTag:     { fontSize:9, letterSpacing:2, fontWeight:700, borderRadius:5, padding:"2px 7px" },
  progBarWrap:{ background:"#18182a", borderRadius:6, height:4, marginBottom:10, overflow:"hidden" },
  progFill:   { height:"100%", borderRadius:6, transition:"width 0.5s ease" },
  routineItem:    { display:"flex", alignItems:"center", gap:12, background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:14, padding:"15px", marginBottom:9, cursor:"pointer", fontSize:13, color:C.text, fontFamily:"'Outfit', sans-serif" },
  routineItemDone:{ opacity:0.45 },
  routineCheck:   { width:24, height:24, borderRadius:50, border:`1px solid ${C.borderHi}`, flexShrink:0, display:"flex", alignItems:"center", justifyContent:"center", fontSize:12, color:C.text },
  routineCheckDone:{ background:C.green, border:"none", boxShadow:`0 0 10px ${C.green}66` },
  lockedIn:       { display:"flex", alignItems:"center", justifyContent:"center", gap:12, marginTop:24, padding:"20px", background:"#081a10", border:`1px solid ${C.green}44`, borderRadius:18, boxShadow:`0 0 32px #10b98122` },
  lockedInText:   { fontFamily:"'Bebas Neue', sans-serif", fontSize:26, letterSpacing:4, color:C.green },
  inputRow:   { display:"flex", gap:8, marginBottom:16 },
  lineInput:  { flex:1, background:C.surfaceHi, border:`1px solid ${C.border}`, borderRadius:14, padding:"14px 16px", color:C.text, fontSize:14, fontFamily:"'Outfit', sans-serif", outline:"none" },
  addBtn:     { width:48, height:48, borderRadius:14, background:`linear-gradient(135deg, #ef4444, #cc2222)`, color:"#fff", border:"none", fontSize:26, cursor:"pointer", boxShadow:`0 4px 16px ${C.accentGlow}` },
  filterRow:  { display:"flex", gap:8, marginBottom:20 },
  filterBtn:  { flex:1, background:"none", border:`1px solid ${C.border}`, borderRadius:10, padding:"8px 0", color:C.sub, cursor:"pointer", fontSize:10, letterSpacing:2, fontFamily:"'Outfit', sans-serif" },
  filterBtnActive:{ background:C.surfaceHi, color:C.text, borderColor:C.borderHi },
  goalPromptsWrap:{ marginBottom:20 },
  goalPromptsLabel:{ fontSize:9, letterSpacing:3, color:C.sub, marginBottom:10, fontWeight:700 },
  goalPromptsList:{ display:"flex", flexWrap:"wrap", gap:7 },
  goalPromptChip: { background:C.surfaceHi, border:`1px solid ${C.border}`, borderRadius:50, padding:"7px 13px", color:"#9898b8", fontSize:12, cursor:"pointer", fontFamily:"'Outfit', sans-serif" },
  goalPromptChipAdded:{ background:"#0a1a10", border:`1px solid ${C.green}55`, color:C.green, cursor:"default" },
  goalRow:    { display:"flex", alignItems:"center", gap:12, background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:16, padding:"15px", marginBottom:9 },
  goalCheck:  { width:26, height:26, borderRadius:50, border:`1px solid ${C.borderHi}`, flexShrink:0, display:"flex", alignItems:"center", justifyContent:"center", fontSize:12, cursor:"pointer" },
  goalCheckDone:{ background:C.accent, border:"none", boxShadow:`0 0 10px ${C.accentGlow}` },
  goalText:   { margin:0, fontSize:14, color:C.text, fontFamily:"'Outfit', sans-serif" },
  goalDate:   { fontSize:11, color:C.sub, margin:"3px 0 0" },
  deleteBtn:  { background:"none", border:"none", color:"#3a3a4a", cursor:"pointer", fontSize:16, padding:"2px 4px" },
  scriptCard: { background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:18, padding:"22px", lineHeight:1.9, fontSize:14, color:"#c0c0dc", marginTop:20, whiteSpace:"pre-wrap" },
  loadingState:{ display:"flex", alignItems:"center", gap:12, color:C.sub, fontSize:14, justifyContent:"center", marginTop:48 },
  loadingDot: { width:8, height:8, borderRadius:50, background:C.sub },
  textarea:   { width:"100%", background:C.surfaceHi, border:`1px solid ${C.border}`, borderRadius:16, padding:"15px 17px", color:C.text, fontSize:14, fontFamily:"'Outfit', sans-serif", resize:"none", boxSizing:"border-box", marginBottom:14, outline:"none", lineHeight:1.65 },
  journalHeader:{ display:"flex", justifyContent:"space-between", alignItems:"center", marginBottom:22 },
  newEntryBtn:{ background:`linear-gradient(135deg, #ef4444, #cc2222)`, color:"#fff", border:"none", borderRadius:12, padding:"9px 18px", fontSize:12, letterSpacing:1, fontWeight:700, cursor:"pointer", fontFamily:"'Outfit', sans-serif", boxShadow:`0 4px 16px ${C.accentGlow}` },
  entryForm:  { background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:20, padding:"20px", marginBottom:20 },
  emojiRow:   { display:"flex", gap:20, marginBottom:18 },
  emojiLabel: { fontSize:9, letterSpacing:2, color:C.sub, marginBottom:9, fontWeight:700 },
  emojiGroup: { display:"flex", gap:7 },
  emojiBtn:   { background:"none", border:`1px solid ${C.border}`, borderRadius:10, padding:"7px 9px", fontSize:18, cursor:"pointer" },
  emojiBtnActive:{ background:"#1a1a30", border:`1px solid ${C.borderHi}` },
  journalCard:{ background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:16, padding:"18px", marginBottom:10 },
  journalCardHeader:{ display:"flex", justifyContent:"space-between", alignItems:"center", marginBottom:12 },
  journalDate:{ fontSize:11, color:C.sub },
  journalText:{ fontSize:14, color:"#b8b8d0", lineHeight:1.7, margin:0 },
  profileHero:{ textAlign:"center", padding:"24px 0 30px" },
  profileAvatar:{ fontSize:72, marginBottom:14, display:"block" },
  profileName:{ fontFamily:"'Bebas Neue', sans-serif", fontSize:42, letterSpacing:4, margin:"0 0 4px" },
  profileSport:{ color:C.sub, fontSize:14 },
  statsRow:   { display:"flex", gap:10, marginBottom:24 },
  statCard:   { flex:1, background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:16, padding:"18px 10px", textAlign:"center" },
  statNum:    { fontFamily:"'Bebas Neue', sans-serif", fontSize:38, color:C.text, lineHeight:1 },
  statLabel:  { fontSize:9, letterSpacing:2, color:C.sub, marginTop:5 },
  aboutCard:  { background:`linear-gradient(135deg, ${C.surface}, ${C.surfaceHi})`, border:`1px solid ${C.border}`, borderRadius:18, padding:"20px" },
  aboutTitle: { fontSize:10, letterSpacing:3, color:C.sub, marginBottom:12, fontWeight:700 },
  aboutText:  { fontSize:13, color:"#787890", lineHeight:1.7, marginBottom:10 },
  backBtn:    { background:"none", border:"none", color:C.sub, cursor:"pointer", fontSize:13, padding:"0 0 16px", fontFamily:"'Outfit', sans-serif", letterSpacing:0.5 },
  emptyState: { color:"#3a3a52", textAlign:"center", marginTop:48, fontSize:13, letterSpacing:0.5 },
