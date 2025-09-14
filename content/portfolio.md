---
title: "Portfolio"
layout: "single"
summary: "Explore my technical projects showcasing expertise in cloud architecture, AI/ML, and full-stack development"
showtoc: false
showReadingTime: false
---

{{< rawhtml >}}
<style>
:root {
    --gradient-a: #0ea5e9; /* sky-500 */
    --gradient-b: #14b8a6; /* teal-500 */
    --gradient-accent: linear-gradient(135deg, var(--gradient-a) 0%, var(--gradient-b) 100%);
  --glass-bg: rgba(255,255,255,0.55);
  --glass-border: rgba(255,255,255,0.35);
  --glass-shadow: 0 8px 32px rgba(31,38,135,0.12);
  --radius-lg: 18px;
  --radius-sm: 6px;
  --transition: 0.35s cubic-bezier(.34,.9,.41,.99);
}
[data-theme='dark'] :root, body.dark :root { /* fallback if theme toggles add class */ }

body.dark .project-card { background: rgba(20,22,34,0.65); backdrop-filter: blur(14px) saturate(140%); }
body.dark .skills-section { background: rgba(40,44,58,0.55); }

.portfolio-intro {
  text-align:center;
  margin:2.2rem 0 3.2rem;
  padding:2.4rem 2rem;
  background:var(--gradient-accent);
  border-radius:var(--radius-lg);
  color:#fff;
  position:relative;
  overflow:hidden;
}
.portfolio-intro:after{
  content:"";position:absolute;inset:0;background:radial-gradient(circle at 75% 15%,rgba(255,255,255,.35),transparent 60%);mix-blend-mode:overlay;pointer-events:none;
}
.portfolio-intro h2{margin:0 0 1rem;font-size:clamp(2rem,4vw,2.75rem);font-weight:700;letter-spacing:.5px;}
.portfolio-intro p{font-size:1.08rem;opacity:.92;margin:0;max-width:780px;margin-inline:auto;line-height:1.5;}

.toolbar {display:flex;flex-wrap:wrap;gap:.75rem;align-items:center;justify-content:center;margin:0 0 2.2rem;}
.filter-btn{background:var(--code-bg);border:1px solid var(--border);padding:.55rem 1rem;border-radius:30px;font-size:.72rem;letter-spacing:.55px;text-transform:uppercase;cursor:pointer;position:relative;font-weight:600;color:var(--secondary);transition:var(--transition);backdrop-filter:blur(6px);} 
.filter-btn.active,.filter-btn:hover{background:var(--gradient-accent);color:#fff;border-color:transparent;box-shadow:0 4px 18px -3px rgba(4,120,180,.45);} 

/* density toggle removed */

.project-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(360px,1fr));gap:2.1rem;margin:2.2rem 0;}

.project-card{background:var(--glass-bg);backdrop-filter:blur(18px) saturate(160%);-webkit-backdrop-filter:blur(18px) saturate(160%);border:1px solid var(--glass-border);border-radius:var(--radius-lg);padding:1.9rem 1.75rem;box-shadow:var(--glass-shadow);transition:var(--transition);position:relative;overflow:hidden;isolation:isolate;display:flex;flex-direction:column;min-height:340px;}
.project-card:before{content:"";position:absolute;inset:0;background:linear-gradient(140deg,rgba(255,255,255,.28),rgba(255,255,255,0) 55%);opacity:.55;pointer-events:none;}
.project-card:after{content:"";position:absolute;top:0;left:0;right:0;height:4px;background:linear-gradient(90deg,var(--gradient-a),var(--gradient-b));opacity:.9;}
.project-card:hover{transform:translateY(-6px) scale(1.012);box-shadow:0 18px 55px -15px rgba(0,0,0,.28);}
/* Removed gradient hover effect on titles as requested */
/* .project-card:hover .project-title{background:linear-gradient(90deg,var(--gradient-a),var(--gradient-b));-webkit-background-clip:text;color:transparent;} */

.badge-row{display:flex;flex-wrap:wrap;gap:.4rem;margin-bottom:.9rem;}
.meta-badge{font-size:.62rem;font-weight:700;letter-spacing:.5px;padding:.35rem .55rem;border-radius:6px;text-transform:uppercase;background:linear-gradient(120deg,#313a5c,#1f2435);color:#cbd5e1;border:1px solid #3c4869;box-shadow:0 1px 3px rgba(0,0,0,.4);}
.meta-badge.accent{background:linear-gradient(120deg,var(--gradient-a),var(--gradient-b));color:#fff;border:none;}
.meta-badge.alt{background:#12a34a;border:none;color:#fff;}

.project-header{margin:0 0 1.1rem;}
.project-title{font-size:1.35rem;font-weight:700;margin:0 0 .55rem;color:var(--primary);line-height:1.25;transition:var(--transition);}
.project-description{color:var(--secondary);line-height:1.55;margin-bottom:1.1rem;font-size:.95rem;}

.tech-stack{display:flex;flex-wrap:wrap;gap:.45rem;margin:.75rem 0 1.1rem;}
.tech-badge{background:linear-gradient(135deg,var(--gradient-a),var(--gradient-b));color:#fff;padding:.42rem .75rem;border-radius:18px;font-size:.72rem;font-weight:600;letter-spacing:.3px;box-shadow:0 2px 6px -1px rgba(118,75,162,.45);}

.skills-section{background:var(--code-bg);padding:.85rem .9rem;border-radius:12px;margin-top:auto;border:1px solid var(--border);}
.skills-title{font-weight:600;margin:0 0 .35rem;color:var(--primary);font-size:.75rem;letter-spacing:.6px;text-transform:uppercase;}
.skills-list{color:var(--secondary);font-size:.72rem;line-height:1.35;}

.project-links{display:flex;gap:.75rem;margin-top:1.05rem;flex-wrap:wrap;}
.project-link{display:inline-flex;align-items:center;gap:.5rem;padding:.55rem 1rem;border-radius:9px;text-decoration:none;font-weight:600;font-size:.73rem;letter-spacing:.3px;transition:var(--transition);position:relative;}
.project-link:focus-visible{outline:2px solid var(--gradient-a);outline-offset:2px;}
.github-link{background:var(--code-bg);color:var(--content);border:1px solid var(--border);}
.github-link:hover{background:#333;color:#fff;}
.demo-link{background:var(--gradient-accent);color:#fff;}
.demo-link:hover{transform:translateY(-2px) scale(1.03);} 
.medium-link{background:#12a34a;color:#fff;}
.medium-link:hover{background:#0e8a3e;}

.enter-anim{opacity:0;transform:translateY(18px) scale(.98);}
.enter-anim.visible{opacity:1;transform:translateY(0) scale(1);transition:var(--transition);}

/* compact view removed */

@media (max-width: 860px){
  .project-grid{grid-template-columns:repeat(auto-fit,minmax(260px,1fr));}
}
@media (max-width: 600px){
  .portfolio-intro{padding:1.8rem 1.2rem;}
  .project-grid{gap:1.4rem;}
  .project-card{padding:1.25rem 1.1rem;}
  .project-title{font-size:1.15rem;}
  .tech-badge{font-size:.65rem;padding:.35rem .6rem;}
}
</style>

<div class="portfolio-intro">
    <h2>🚀 Technical Portfolio</h2>
    <p>Innovative projects showcasing full-stack development, cloud architecture, and AI/ML expertise</p>
</div>
{{< /rawhtml >}}

{{< rawhtml >}}
<div class="toolbar" id="portfolioToolbar">
  <button class="filter-btn active" data-filter="all">All</button>
  <button class="filter-btn" data-filter="ai">AI / RAG</button>
  <button class="filter-btn" data-filter="kubernetes">Kubernetes</button>
  <button class="filter-btn" data-filter="backend">Backend</button>
  <button class="filter-btn" data-filter="realtime">Real-time</button>
  <button class="filter-btn" data-filter="competition">Competition</button>
  
</div>
<div class="project-grid">

<div class="project-card enter-anim" data-category="backend kubernetes" data-repo="url-shortener-system-design">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge accent">Featured</span><span class="meta-badge">K8s</span><span class="meta-badge">Caching</span></div>
        <h3 class="project-title">🔗 Scalable URL Shortener</h3>
    </div>
    
    <div class="project-description">
        A production-ready URL shortener application implementing classic system design patterns. Built with FastAPI and deployed on Kubernetes with MongoDB and Redis for optimal performance and scalability.
    </div>
    
    <div class="tech-stack">
        <span class="tech-badge">FastAPI</span>
        <span class="tech-badge">MongoDB</span>
        <span class="tech-badge">Redis</span>
        <span class="tech-badge">Kubernetes</span>
        <span class="tech-badge">Docker</span>
        <span class="tech-badge">GitHub Actions</span>
    </div>
    
    <div class="skills-section">
        <div class="skills-title">Key Skills Demonstrated:</div>
        <div class="skills-list">System Design • API Development • Containerization • CI/CD • Caching Strategies • Microservices Architecture</div>
    </div>
    
    <div class="project-links">
        <a href="https://github.com/VarunArora14/url-shortener-system-design" class="project-link github-link" target="_blank">
            📂 View Code
        </a>
        <a href="https://medium.com/@varunarora1408/url-shortener-system-design-implementation-part-1-afd97618d917" class="project-link medium-link" target="_blank">
            📖 Part 1 - Architecture
        </a>
        <a href="https://medium.com/@varunarora1408/url-shortener-system-design-implementation-part-2-0bd9b7a27742" class="project-link medium-link" target="_blank">
            📖 Part 2 - Deployment
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="realtime ai" data-repo="align">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge accent">Mobile</span><span class="meta-badge">AI</span></div>
        <h3 class="project-title">📅 Align – Smart Reminders</h3>
    </div>
    
    <div class="project-description">
        React Native + Expo app for natural-language reminders and lightweight daily goals. Parses phrases like “Call mom tomorrow at 2pm”, schedules local notifications, and stores data on-device with SQLite.
    </div>
    
    <div class="tech-stack">
        <span class="tech-badge">React Native</span>
        <span class="tech-badge">Expo</span>
        <span class="tech-badge">TypeScript</span>
        <span class="tech-badge">NativeWind</span>
        <span class="tech-badge">SQLite</span>
        <span class="tech-badge">LangChain</span>
    </div>
    
    <div class="skills-section">
        <div class="skills-title">Key Skills Demonstrated:</div>
        <div class="skills-list">Mobile Dev • Push Notifications • AI-assisted Parsing • Local Persistence • Testing with Jest</div>
    </div>
    
    <div class="project-links">
        <a href="https://github.com/VarunArora14/align" class="project-link github-link" target="_blank">
            📂 View Code
        </a>
        <a href="https://github.com/VarunArora14/align#readme" class="project-link demo-link" target="_blank">
            📖 README & Screenshots
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="ai rag" data-repo="Personal-Blog-Chatbot">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge accent">Live</span><span class="meta-badge">RAG</span></div>
        <h3 class="project-title">🤖 Personal Blog Chatbot</h3>
    </div>
    
    <div class="project-description">
        An intelligent RAG-powered chatbot that answers questions based on my personal blog content. Features advanced retrieval techniques and provides source attribution for transparency and verification.
    </div>
    
    <div class="tech-stack">
        <span class="tech-badge">Python</span>
        <span class="tech-badge">LangChain</span>
        <span class="tech-badge">Google Gemini</span>
        <span class="tech-badge">RAG</span>
        <span class="tech-badge">Streamlit</span>
        <span class="tech-badge">Vector DB</span>
    </div>
    
    <div class="skills-section">
        <div class="skills-title">Key Skills Demonstrated:</div>
        <div class="skills-list">Advanced RAG • Data Processing • NLP • API Integration • Web Scraping • Deployment</div>
    </div>
    
    <div class="project-links">
        <a href="https://github.com/VarunArora14/Personal-Blog-Chatbot" class="project-link github-link" target="_blank">
            📂 View Code
        </a>
        <a href="https://personal-blog-chatbot.streamlit.app/" class="project-link demo-link" target="_blank">
            🚀 Live Demo
        </a>
        <a href="https://medium.com/@varunarora1408/building-rag-chatbot-on-my-personal-blog-ff89ac772c69" class="project-link medium-link" target="_blank">
            📖 Technical Article
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="ai kubernetes rag competition" data-repo="Kubernetes-Documentation-Bot">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge alt">Competition</span><span class="meta-badge">HyDE</span><span class="meta-badge">QD</span></div>
        <h3 class="project-title">📚 Kubernetes Documentation Bot</h3>
    </div>
    
    <div class="project-description">
        A kubernetes documentation assistant implementing HyDE and Query Decomposition RAG techniques. Submitted to Google Gemini API Developer Competition 2024 for its innovative approach to technical documentation retrieval.
    </div>
    
    <div class="tech-stack">
        <span class="tech-badge">Python</span>
        <span class="tech-badge">LangChain</span>
        <span class="tech-badge">Google Gemini</span>
        <span class="tech-badge">RAG</span>
        <span class="tech-badge">Docker</span>
        <span class="tech-badge">Streamlit</span>
    </div>
    
    <div class="skills-section">
        <div class="skills-title">Key Skills Demonstrated:</div>
        <div class="skills-list">Advanced RAG Techniques • Markdown Processing • Microservices • API Design • Containerization</div>
    </div>
    
    <div class="project-links">
        <a href="https://github.com/VarunArora14/Kubernetes-Documentation-Bot" class="project-link github-link" target="_blank">
            📂 View Code
        </a>
        <a href="https://ai.google.dev/competition/projects/varun-arora" class="project-link demo-link" target="_blank">
            🏆 Competition Entry
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="ai rag" data-repo="GenAI-Cookbook">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge">Playground</span><span class="meta-badge">Agents</span></div>
        <h3 class="project-title">🧠 GenAI Cookbook</h3>
    </div>
    
    <div class="project-description">
        A comprehensive repository of GenAI implementations featuring various RAG techniques, AI agents, and reusable code snippets. Serves as a practical guide for building production-ready GenAI applications.
    </div>
    
    <div class="tech-stack">
        <span class="tech-badge">Python</span>
        <span class="tech-badge">LangChain</span>
        <span class="tech-badge">RAG</span>
        <span class="tech-badge">AI Agents</span>
        <span class="tech-badge">Hugging Face</span>
        <span class="tech-badge">Vector DBs</span>
    </div>
    
    <div class="skills-section">
        <div class="skills-title">Key Skills Demonstrated:</div>
        <div class="skills-list">AI/ML Engineering • Data Pipeline Design • Vector Database Configuration • Agent Orchestration</div>
    </div>
    
    <div class="project-links">
        <a href="https://github.com/VarunArora14/GenAI-Cookbook" class="project-link github-link" target="_blank">
            📂 View Code
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="realtime backend" data-repo="vdocs">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge">Realtime</span><span class="meta-badge">Collab</span></div>
        <h3 class="project-title">📝 vDocs - Collaborative Editor</h3>
    </div>
    
    <div class="project-description">
        A full-stack collaborative document management platform with real-time editing capabilities. Features Google authentication, cross-platform mobile support, and seamless real-time collaboration.
    </div>
    
    <div class="tech-stack">
        <span class="tech-badge">Node.js</span>
        <span class="tech-badge">Flutter</span>
        <span class="tech-badge">Socket.io</span>
        <span class="tech-badge">Google Cloud</span>
        <span class="tech-badge">MongoDB</span>
        <span class="tech-badge">Riverpod</span>
    </div>
    
    <div class="skills-section">
        <div class="skills-title">Key Skills Demonstrated:</div>
        <div class="skills-list">Real-time Systems • Mobile Development • Authentication • State Management • Cloud Integration</div>
    </div>
    
    <div class="project-links">
        <a href="https://github.com/VarunArora14/vdocs" class="project-link github-link" target="_blank">
            📂 View Code
        </a>
    </div>
</div>

</div>
{{< /rawhtml >}}

---

{{< rawhtml >}}
<div style="text-align: center; margin: 3rem 0; padding: 2rem; background: var(--code-bg); border-radius: 15px;">
    <h3 style="margin-bottom: 1rem; color: var(--primary);">💼 Looking for Collaboration?</h3>
    <p style="margin-bottom: 1.5rem; color: var(--secondary);">I'm always interested in discussing new opportunities, technical challenges, and innovative projects.</p>
    <div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;">
        <a href="https://github.com/VarunArora14" style="display: inline-flex; align-items: center; gap: 0.5rem; padding: 0.8rem 1.5rem; background: #333; color: white; text-decoration: none; border-radius: 8px; font-weight: 500;" target="_blank">
            🔗 GitHub Profile
        </a>
        <a href="https://medium.com/@varunarora1408" style="display: inline-flex; align-items: center; gap: 0.5rem; padding: 0.8rem 1.5rem; background: #12a34a; color: white; text-decoration: none; border-radius: 8px; font-weight: 500;" target="_blank">
            ✍️ Technical Blog
        </a>
        <a href="https://varunarora14.github.io/" style="display: inline-flex; align-items: center; gap: 0.5rem; padding: 0.8rem 1.5rem; background: linear-gradient(135deg, #667eea, #764ba2); color: white; text-decoration: none; border-radius: 8px; font-weight: 500;" target="_blank">
            🌐 Full Blog
        </a>
    </div>
</div>
{{< /rawhtml >}}

{{< rawhtml >}}
<script>
(function(){
    const cards = Array.from(document.querySelectorAll('.project-card'));
    const filterBtns = document.querySelectorAll('.filter-btn');

    // Filtering
    filterBtns.forEach(btn=>btn.addEventListener('click',()=>{
        filterBtns.forEach(b=>b.classList.remove('active'));
        btn.classList.add('active');
        const f = btn.dataset.filter;
        cards.forEach(c=>{
            if(f==='all' || (c.dataset.category||'').includes(f)){
                c.style.display='flex';
                requestAnimationFrame(()=>c.classList.add('visible'));
            } else {
                c.style.display='none';
            }
        });
    }));

    // Entrance animation
    const io = new IntersectionObserver(entries=>{
        entries.forEach(e=>{ if(e.isIntersecting){ e.target.classList.add('visible'); io.unobserve(e.target);} });
    },{threshold:.18});
    cards.forEach(c=>io.observe(c));

    // GitHub stats
    async function fetchRepo(repo){
        try{const r=await fetch(`https://api.github.com/repos/VarunArora14/${repo}`); if(!r.ok) return null; return await r.json();}catch{return null;}
    }
    async function initStats(){
        for(const card of cards){
            const repo = card.dataset.repo; if(!repo) continue;
            const data = await fetchRepo(repo); if(!data) continue;
            const infoBar = document.createElement('div');
            infoBar.className='repo-stats';
            infoBar.style.display='flex';infoBar.style.alignItems='center';infoBar.style.gap='0.85rem';infoBar.style.marginTop='0.85rem';infoBar.style.fontSize='.66rem';infoBar.style.letterSpacing='.5px';infoBar.style.opacity='.85';infoBar.style.flexWrap='wrap';
            infoBar.innerHTML = `<span title="Stars">⭐ ${data.stargazers_count}</span><span title="Last Push">🕒 ${new Date(data.pushed_at).toLocaleDateString(undefined,{month:'short',day:'numeric',year:'numeric'})}</span>`;
            const links = card.querySelector('.project-links');
            if(links) links.parentNode.insertBefore(infoBar, links);
        }
    }
    initStats();
})();
</script>
{{< /rawhtml >}}
