import { useState, useEffect, useRef } from "react";

const styles = `
  @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=DM+Sans:wght@300;400;500&display=swap');

  * { margin: 0; padding: 0; box-sizing: border-box; }

  :root {
    --cream: #F5F0E8;
    --warm-white: #FDFAF5;
    --charcoal: #1C1C1A;
    --sand: #C8B89A;
    --terracotta: #C17F5A;
    --sage: #7A8C72;
    --deep: #2A2420;
    --muted: #8A8278;
  }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--warm-white);
    color: var(--charcoal);
    overflow-x: hidden;
  }

  .grain {
    position: fixed; inset: 0; pointer-events: none; z-index: 100; opacity: 0.03;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  }

  /* NAV */
  nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 50;
    display: flex; align-items: center; justify-content: space-between;
    padding: 1.5rem 4rem;
    background: rgba(253, 250, 245, 0.85);
    backdrop-filter: blur(12px);
    border-bottom: 1px solid rgba(200, 184, 154, 0.2);
  }
  .nav-logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.5rem; font-weight: 300; letter-spacing: 0.15em;
    color: var(--charcoal);
  }
  .nav-logo span { color: var(--terracotta); }
  .nav-links { display: flex; gap: 2.5rem; }
  .nav-links a {
    font-size: 0.75rem; letter-spacing: 0.12em; text-transform: uppercase;
    color: var(--muted); text-decoration: none; transition: color 0.2s;
    cursor: pointer;
  }
  .nav-links a:hover { color: var(--charcoal); }
  .nav-cta {
    font-size: 0.75rem; letter-spacing: 0.1em; text-transform: uppercase;
    padding: 0.6rem 1.5rem; background: var(--charcoal); color: var(--cream);
    border: none; cursor: pointer; transition: background 0.2s;
  }
  .nav-cta:hover { background: var(--terracotta); }

  /* HERO */
  .hero {
    min-height: 100vh; display: grid; grid-template-columns: 1fr 1fr;
    padding-top: 5rem;
  }
  .hero-left {
    display: flex; flex-direction: column; justify-content: center;
    padding: 6rem 4rem 6rem 5rem;
    background: var(--warm-white);
  }
  .hero-eyebrow {
    font-size: 0.7rem; letter-spacing: 0.2em; text-transform: uppercase;
    color: var(--terracotta); margin-bottom: 1.5rem;
    display: flex; align-items: center; gap: 0.75rem;
  }
  .hero-eyebrow::before {
    content: ''; display: block; width: 2rem; height: 1px; background: var(--terracotta);
  }
  .hero-title {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(3rem, 5vw, 4.5rem);
    font-weight: 300; line-height: 1.1;
    margin-bottom: 1.5rem; color: var(--charcoal);
  }
  .hero-title em { font-style: italic; color: var(--terracotta); }
  .hero-desc {
    font-size: 0.9rem; line-height: 1.8; color: var(--muted);
    max-width: 420px; margin-bottom: 3rem;
  }
  .hero-actions { display: flex; gap: 1rem; align-items: center; }
  .btn-primary {
    padding: 0.9rem 2.5rem; background: var(--charcoal); color: var(--cream);
    font-size: 0.75rem; letter-spacing: 0.12em; text-transform: uppercase;
    border: none; cursor: pointer; transition: all 0.25s;
  }
  .btn-primary:hover { background: var(--terracotta); transform: translateY(-1px); }
  .btn-ghost {
    padding: 0.9rem 2rem; background: transparent; color: var(--charcoal);
    font-size: 0.75rem; letter-spacing: 0.12em; text-transform: uppercase;
    border: 1px solid var(--sand); cursor: pointer; transition: all 0.25s;
  }
  .btn-ghost:hover { border-color: var(--charcoal); }

  .hero-right {
    position: relative; overflow: hidden;
    background: var(--cream);
  }
  .hero-img-grid {
    display: grid; grid-template-rows: 1fr 1fr; height: 100%;
  }
  .hero-img-top {
    background: linear-gradient(135deg, #d4c4a8 0%, #b8a88a 100%);
    display: flex; align-items: center; justify-content: center;
    position: relative; overflow: hidden;
  }
  .hero-img-bottom {
    display: grid; grid-template-columns: 1fr 1fr;
  }
  .hero-img-bottom-l {
    background: linear-gradient(135deg, #7A8C72 0%, #5a6b52 100%);
    display: flex; align-items: center; justify-content: center;
  }
  .hero-img-bottom-r {
    background: linear-gradient(135deg, #C17F5A 0%, #a06040 100%);
    display: flex; align-items: center; justify-content: center;
  }
  .room-label {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.1rem; font-weight: 300; letter-spacing: 0.1em;
    color: rgba(255,255,255,0.9);
  }
  .badge {
    position: absolute; bottom: 1.5rem; right: 1.5rem;
    background: var(--charcoal); color: var(--cream);
    padding: 0.5rem 1rem;
    font-size: 0.65rem; letter-spacing: 0.1em; text-transform: uppercase;
  }

  /* STATS */
  .stats-bar {
    background: var(--charcoal); padding: 2rem 5rem;
    display: flex; justify-content: space-around; align-items: center;
  }
  .stat { text-align: center; }
  .stat-num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2.2rem; font-weight: 300; color: var(--cream);
  }
  .stat-label {
    font-size: 0.65rem; letter-spacing: 0.15em; text-transform: uppercase;
    color: var(--sand); margin-top: 0.25rem;
  }
  .stat-divider { width: 1px; height: 3rem; background: rgba(200,184,154,0.3); }

  /* AI SECTION */
  .ai-section {
    padding: 7rem 5rem;
    background: var(--warm-white);
  }
  .section-header { text-align: center; margin-bottom: 4rem; }
  .section-eyebrow {
    font-size: 0.7rem; letter-spacing: 0.2em; text-transform: uppercase;
    color: var(--terracotta); margin-bottom: 1rem;
  }
  .section-title {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(2rem, 3.5vw, 3rem);
    font-weight: 300; color: var(--charcoal); margin-bottom: 1rem;
  }
  .section-sub {
    font-size: 0.85rem; color: var(--muted); line-height: 1.7; max-width: 500px; margin: 0 auto;
  }

  /* AI CHAT BOX */
  .ai-box {
    max-width: 800px; margin: 0 auto;
    border: 1px solid rgba(200,184,154,0.4);
    background: var(--warm-white);
    overflow: hidden;
  }
  .ai-header {
    background: var(--charcoal); padding: 1rem 1.5rem;
    display: flex; align-items: center; gap: 0.75rem;
  }
  .ai-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--terracotta); }
  .ai-header-title {
    font-size: 0.7rem; letter-spacing: 0.15em; text-transform: uppercase;
    color: var(--sand);
  }
  .ai-messages {
    padding: 1.5rem; min-height: 280px; max-height: 340px;
    overflow-y: auto; display: flex; flex-direction: column; gap: 1rem;
  }
  .ai-messages::-webkit-scrollbar { width: 4px; }
  .ai-messages::-webkit-scrollbar-track { background: transparent; }
  .ai-messages::-webkit-scrollbar-thumb { background: var(--sand); }

  .msg { display: flex; gap: 0.75rem; animation: fadeUp 0.3s ease; }
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(8px); }
    to { opacity: 1; transform: translateY(0); }
  }
  .msg.user { flex-direction: row-reverse; }
  .msg-avatar {
    width: 32px; height: 32px; border-radius: 50%; flex-shrink: 0;
    display: flex; align-items: center; justify-content: center;
    font-size: 0.65rem; font-weight: 500; letter-spacing: 0.05em;
  }
  .msg-avatar.ai { background: var(--charcoal); color: var(--cream); }
  .msg-avatar.user { background: var(--terracotta); color: var(--cream); }
  .msg-bubble {
    max-width: 75%; padding: 0.75rem 1rem;
    font-size: 0.82rem; line-height: 1.65; border-radius: 2px;
  }
  .msg.ai .msg-bubble { background: var(--cream); color: var(--charcoal); }
  .msg.user .msg-bubble { background: var(--terracotta); color: white; }
  .typing { display: flex; gap: 4px; padding: 0.75rem 1rem; background: var(--cream); width: fit-content; }
  .typing span {
    width: 6px; height: 6px; border-radius: 50%;
    background: var(--sand); animation: bounce 1.2s infinite;
  }
  .typing span:nth-child(2) { animation-delay: 0.2s; }
  .typing span:nth-child(3) { animation-delay: 0.4s; }
  @keyframes bounce {
    0%, 60%, 100% { transform: translateY(0); }
    30% { transform: translateY(-6px); }
  }

  .ai-input-row {
    border-top: 1px solid rgba(200,184,154,0.3);
    display: flex; align-items: center; padding: 0.75rem 1rem; gap: 0.75rem;
    background: var(--cream);
  }
  .ai-input {
    flex: 1; border: none; background: transparent; outline: none;
    font-family: 'DM Sans', sans-serif; font-size: 0.85rem;
    color: var(--charcoal);
  }
  .ai-input::placeholder { color: var(--muted); }
  .ai-send {
    background: var(--charcoal); color: var(--cream);
    border: none; padding: 0.5rem 1.2rem; font-size: 0.7rem;
    letter-spacing: 0.1em; text-transform: uppercase; cursor: pointer;
    transition: background 0.2s;
  }
  .ai-send:hover { background: var(--terracotta); }
  .ai-send:disabled { opacity: 0.5; cursor: not-allowed; }

  .suggestions {
    padding: 1rem 1.5rem; border-top: 1px solid rgba(200,184,154,0.2);
    display: flex; gap: 0.5rem; flex-wrap: wrap;
  }
  .chip {
    font-size: 0.7rem; letter-spacing: 0.05em;
    padding: 0.35rem 0.85rem;
    border: 1px solid rgba(200,184,154,0.5);
    background: transparent; cursor: pointer; color: var(--muted);
    transition: all 0.2s;
  }
  .chip:hover { border-color: var(--terracotta); color: var(--terracotta); }

  /* SERVICES */
  .services {
    padding: 7rem 5rem;
    background: var(--cream);
  }
  .services-grid {
    display: grid; grid-template-columns: repeat(3, 1fr); gap: 2px;
    margin-top: 4rem;
  }
  .service-card {
    background: var(--warm-white); padding: 2.5rem;
    border-bottom: 3px solid transparent; transition: all 0.3s;
    cursor: pointer;
  }
  .service-card:hover { border-bottom-color: var(--terracotta); transform: translateY(-4px); }
  .service-num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 3rem; font-weight: 300; color: var(--sand);
    line-height: 1; margin-bottom: 1.5rem;
  }
  .service-name {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.4rem; font-weight: 400; margin-bottom: 0.75rem;
  }
  .service-desc {
    font-size: 0.8rem; line-height: 1.7; color: var(--muted);
  }

  /* FOOTER */
  footer {
    background: var(--deep); color: var(--cream); padding: 3rem 5rem;
    display: flex; justify-content: space-between; align-items: center;
  }
  .footer-logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.3rem; font-weight: 300; letter-spacing: 0.15em;
  }
  .footer-logo span { color: var(--terracotta); }
  .footer-links { display: flex; gap: 2rem; }
  .footer-links a {
    font-size: 0.7rem; letter-spacing: 0.1em; text-transform: uppercase;
    color: var(--sand); text-decoration: none; cursor: pointer;
    transition: color 0.2s;
  }
  .footer-links a:hover { color: var(--cream); }
  .footer-copy { font-size: 0.7rem; color: var(--muted); }

  @media (max-width: 768px) {
    nav { padding: 1rem 1.5rem; }
    .nav-links { display: none; }
    .hero { grid-template-columns: 1fr; }
    .hero-right { min-height: 50vh; }
    .hero-left { padding: 3rem 1.5rem; }
    .stats-bar { padding: 2rem 1.5rem; flex-wrap: wrap; gap: 1.5rem; }
    .ai-section, .services { padding: 4rem 1.5rem; }
    .services-grid { grid-template-columns: 1fr; }
    footer { flex-direction: column; gap: 1.5rem; text-align: center; }
    .footer-links { flex-wrap: wrap; justify-content: center; }
  }
`;

const SUGGESTIONS = [
  "Minimalist Scandinavian living room",
  "Bohemian bedroom palette",
  "Modern kitchen with warm tones",
  "Japandi home office setup",
];

const SERVICES = [
  { num: "01", name: "AI Style Consultation", desc: "Describe your vision and let our AI generate personalized design concepts, color palettes, and furniture recommendations tailored to your lifestyle." },
  { num: "02", name: "Space Planning", desc: "Upload your floor plan and receive intelligent layout suggestions that maximize flow, light, and functionality for every room." },
  { num: "03", name: "Material & Finish Curation", desc: "Explore curated collections of textures, fabrics, and finishes. Our AI matches materials to your aesthetic and budget preferences." },
  { num: "04", name: "Mood Board Generation", desc: "Generate beautiful, cohesive mood boards in seconds. Save, share, and refine with real-time AI feedback." },
  { num: "05", name: "3D Visualization", desc: "Transform 2D concepts into immersive 3D renders. See your redesigned space before making a single purchase." },
  { num: "06", name: "Vendor Matching", desc: "Get connected with vetted local designers, contractors, and furniture suppliers aligned with your project scope and budget." },
];

export default function InteriorDesignWebsite() {
  const [messages, setMessages] = useState([
    {
      role: "ai",
      text: "Welcome to HAUS AI. I'm your personal interior design assistant. Describe a room you'd like to transform, and I'll suggest design directions, palettes, and styles.",
    },
  ]);
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const messagesEndRef = useRef(null);

  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  }, [messages, loading]);

  const sendMessage = async (text) => {
    const userText = text || input.trim();
    if (!userText || loading) return;
    setInput("");
    setMessages((prev) => [...prev, { role: "user", text: userText }]);
    setLoading(true);

    try {
      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1000,
          system: `You are HAUS AI, an elegant interior design consultant with refined taste. You help clients discover their interior design style and give thoughtful, specific design advice. 

Keep responses concise (2-4 sentences), warm, and sophisticated. Focus on:
- Color palettes with specific names (e.g., "warm terracotta", "dusty sage", "aged brass")
- Style references (Japandi, Wabi-sabi, Art Deco, etc.)
- Key furniture pieces or materials
- Mood and atmosphere

Never use bullet points. Write in flowing, editorial prose. Be inspiring and specific.`,
          messages: [
            ...messages.filter((m) => m.role !== "ai" || messages.indexOf(m) !== 0).map((m) => ({
              role: m.role === "ai" ? "assistant" : "user",
              content: m.text,
            })),
            { role: "user", content: userText },
          ],
        }),
      });
      const data = await response.json();
      const reply = data.content?.find((b) => b.type === "text")?.text || "I'd love to help refine your vision further.";
      setMessages((prev) => [...prev, { role: "ai", text: reply }]);
    } catch {
      setMessages((prev) => [...prev, { role: "ai", text: "Apologies — please try again in a moment." }]);
    }
    setLoading(false);
  };

  return (
    <>
      <style>{styles}</style>
      <div className="grain" />

      {/* NAV */}
      <nav>
        <div className="nav-logo">HA<span>U</span>S</div>
        <div className="nav-links">
          <a>Portfolio</a>
          <a>Services</a>
          <a>AI Studio</a>
          <a>About</a>
        </div>
        <button className="nav-cta">Start Project</button>
      </nav>

      {/* HERO */}
      <section className="hero">
        <div className="hero-left">
          <div className="hero-eyebrow">AI-Powered Interior Design</div>
          <h1 className="hero-title">
            Spaces that tell<br />
            <em>your</em> story,<br />
            beautifully
          </h1>
          <p className="hero-desc">
            HAUS combines the discernment of a seasoned designer with the intelligence of AI — crafting interiors that feel intentional, timeless, and unmistakably yours.
          </p>
          <div className="hero-actions">
            <button className="btn-primary" onClick={() => document.querySelector('.ai-section').scrollIntoView({ behavior: 'smooth' })}>
              Talk to AI Designer
            </button>
            <button className="btn-ghost">View Portfolio</button>
          </div>
        </div>
        <div className="hero-right">
          <div className="hero-img-grid">
            <div className="hero-img-top">
              <span className="room-label">Living Room</span>
              <div className="badge">Featured</div>
            </div>
            <div className="hero-img-bottom">
              <div className="hero-img-bottom-l">
                <span className="room-label">Study</span>
              </div>
              <div className="hero-img-bottom-r">
                <span className="room-label">Kitchen</span>
              </div>
            </div>
          </div>
        </div>
      </section>

      {/* STATS */}
      <div className="stats-bar">
        {[["2,400+", "Projects Completed"], ["98%", "Client Satisfaction"], ["45", "Design Awards"], ["12", "Years of Craft"]].map(([num, label], i, arr) => (
          <>
            <div className="stat" key={label}>
              <div className="stat-num">{num}</div>
              <div className="stat-label">{label}</div>
            </div>
            {i < arr.length - 1 && <div className="stat-divider" />}
          </>
        ))}
      </div>

      {/* AI SECTION */}
      <section className="ai-section">
        <div className="section-header">
          <div className="section-eyebrow">Powered by Claude AI</div>
          <h2 className="section-title">Design intelligence, at your fingertips</h2>
          <p className="section-sub">Describe any space and receive thoughtful, curated design guidance in seconds.</p>
        </div>

        <div className="ai-box">
          <div className="ai-header">
            <div className="ai-dot" />
            <span className="ai-header-title">HAUS AI · Design Consultant</span>
          </div>

          <div className="ai-messages">
            {messages.map((m, i) => (
              <div key={i} className={`msg ${m.role}`}>
                <div className={`msg-avatar ${m.role}`}>{m.role === "ai" ? "AI" : "You"}</div>
                <div className="msg-bubble">{m.text}</div>
              </div>
            ))}
            {loading && (
              <div className="msg ai">
                <div className="msg-avatar ai">AI</div>
                <div className="typing">
                  <span /><span /><span />
                </div>
              </div>
            )}
            <div ref={messagesEndRef} />
          </div>

          <div className="suggestions">
            {SUGGESTIONS.map((s) => (
              <button key={s} className="chip" onClick={() => sendMessage(s)}>{s}</button>
            ))}
          </div>

          <div className="ai-input-row">
            <input
              className="ai-input"
              placeholder="Describe your dream space..."
              value={input}
              onChange={(e) => setInput(e.target.value)}
              onKeyDown={(e) => e.key === "Enter" && sendMessage()}
            />
            <button className="ai-send" onClick={() => sendMessage()} disabled={loading || !input.trim()}>
              Send
            </button>
          </div>
        </div>
      </section>

      {/* SERVICES */}
      <section className="services">
        <div className="section-header">
          <div className="section-eyebrow">What We Offer</div>
          <h2 className="section-title">A complete design ecosystem</h2>
          <p className="section-sub">From first concept to final installation, every touchpoint is thoughtfully supported.</p>
        </div>
        <div className="services-grid">
          {SERVICES.map
