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
  --accent: #3b82f6;
  --accent-light: #60a5fa;
  --radius: 8px;
  --card-bg: var(--entry, #fff);
  --card-border: var(--border, #e5e7eb);
  --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
}

[data-theme='dark'] {
  --card-bg: var(--entry, #1d1e20);
  --card-border: #374151;
}

.portfolio-intro {
  text-align: center;
  margin: 3rem 0;
  padding: 1rem;
}

.portfolio-intro h2 {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  background: linear-gradient(120deg, var(--accent), var(--accent-light));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  display: inline-block;
  font-weight: 800;
}

.portfolio-intro p {
  color: var(--secondary);
  font-size: 1.2rem;
  max-width: 600px;
  margin: 0 auto;
  line-height: 1.6;
}

.toolbar {
  display: flex;
  justify-content: center;
  gap: 0.8rem;
  flex-wrap: wrap;
  margin-bottom: 3rem;
}

.filter-btn {
  background: transparent;
  border: 1px solid var(--border);
  color: var(--secondary);
  padding: 0.5rem 1.2rem;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.2s ease;
  font-weight: 500;
}

.filter-btn:hover, .filter-btn.active {
  background: var(--accent);
  color: white;
  border-color: var(--accent);
}

.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(340px, 1fr));
  gap: 2rem;
}

.project-card {
  background: var(--card-bg);
  border: 1px solid var(--card-border);
  border-radius: var(--radius);
  padding: 1.75rem;
  display: flex;
  flex-direction: column;
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease;
}

.project-card:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow);
  border-color: var(--accent);
}

.project-header {
  margin-bottom: 1rem;
}

.project-title {
  margin: 0.75rem 0;
  font-size: 1.4rem;
  color: var(--primary);
  font-weight: 700;
}

.badge-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
  flex-wrap: wrap;
}

.meta-badge {
  font-size: 0.75rem;
  padding: 0.25rem 0.6rem;
  border-radius: 20px;
  background: var(--tertiary);
  color: var(--secondary);
  font-weight: 500;
  border: 1px solid var(--border);
}

.meta-badge.accent {
  background: var(--accent);
  color: white;
  border-color: var(--accent);
}

.project-description {
  font-size: 1rem;
  color: var(--secondary);
  margin-bottom: 1.5rem;
  flex-grow: 1;
  line-height: 1.7;
}

.tech-stack {
  display: flex;
  flex-wrap: wrap;
  gap: 0.6rem;
  margin-bottom: 1.5rem;
}

.tech-badge {
  font-size: 0.8rem;
  padding: 0.3rem 0.8rem;
  border-radius: 6px;
  background: var(--tertiary);
  color: var(--primary);
  border: 1px solid transparent;
}
.project-card:hover .tech-badge {
    border-color: var(--border);
}

.skills-section {
  background: var(--tertiary);
  padding: 1rem;
  border-radius: 8px;
  margin-bottom: 1.5rem;
  font-size: 0.9rem;
  border: 1px solid transparent;
}
[data-theme='dark'] .skills-section { background: rgba(255,255,255,0.03); }

.skills-title {
  font-weight: 600;
  margin-bottom: 0.4rem;
  color: var(--primary);
  font-size: 0.8rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.skills-list {
  color: var(--secondary);
}

.project-links {
  display: flex;
  gap: 1.2rem;
  margin-top: auto;
  align-items: center;
}

.project-link {
  font-size: 0.95rem;
  font-weight: 600;
  color: var(--accent);
  text-decoration: none;
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  transition: opacity 0.2s;
}

.project-link:hover {
  opacity: 0.8;
}

/* GitHub repo stats styling */
.repo-stats {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-top: 1rem;
    font-size: 0.85rem;
    color: var(--secondary);
}

.enter-anim {
    animation: fadeIn 0.4s ease-out forwards;
    opacity: 0; 
}
.enter-anim.visible {
    opacity: 1;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
}

@media (max-width: 768px) {
  .project-grid {
    grid-template-columns: 1fr;
  }
  .portfolio-intro h2 { font-size: 2rem; }
}
</style>

<div class="portfolio-intro">
    <h2>Technical Portfolio</h2>
    <p>Innovative projects on backend, data engineering and AI</p>
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
        <h3 class="project-title">Scalable URL Shortener</h3>
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
            View Code
        </a>
        <a href="https://medium.com/@varunarora1408/url-shortener-system-design-implementation-part-1-afd97618d917" class="project-link medium-link" target="_blank">
            Part 1 - Architecture
        </a>
        <a href="https://medium.com/@varunarora1408/url-shortener-system-design-implementation-part-2-0bd9b7a27742" class="project-link medium-link" target="_blank">
            Part 2 - Deployment
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="realtime backend" data-repo="vdocs">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge">Realtime</span><span class="meta-badge">Collab</span></div>
        <h3 class="project-title">vDocs - Collaborative Editor</h3>
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
            View Code
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="realtime ai" data-repo="align">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge accent">Mobile</span><span class="meta-badge">AI</span></div>
        <h3 class="project-title">Align – Smart Reminders</h3>
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
            View Code
        </a>
        <a href="https://github.com/VarunArora14/align#readme" class="project-link demo-link" target="_blank">
            README & Screenshots
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="ai rag" data-repo="Personal-Blog-Chatbot">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge accent">Live</span><span class="meta-badge">RAG</span></div>
        <h3 class="project-title">Personal Blog Chatbot</h3>
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
            View Code
        </a>
        <a href="https://personal-blog-chatbot.streamlit.app/" class="project-link demo-link" target="_blank">
            Live Demo
        </a>
        <a href="https://medium.com/@varunarora1408/building-rag-chatbot-on-my-personal-blog-ff89ac772c69" class="project-link medium-link" target="_blank">
            Technical Article
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="ai kubernetes rag competition" data-repo="Kubernetes-Documentation-Bot">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge alt">Competition</span><span class="meta-badge">HyDE</span><span class="meta-badge">QD</span></div>
        <h3 class="project-title">Kubernetes Documentation Bot</h3>
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
            View Code
        </a>
        <a href="https://ai.google.dev/competition/projects/varun-arora" class="project-link demo-link" target="_blank">
            Competition Entry
        </a>
    </div>
</div>

<div class="project-card enter-anim" data-category="ai rag" data-repo="GenAI-Cookbook">
    <div class="project-header">
        <div class="badge-row"><span class="meta-badge">Playground</span><span class="meta-badge">Agents</span></div>
        <h3 class="project-title">GenAI Cookbook</h3>
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
            View Code
        </a>
    </div>
</div>



</div>
{{< /rawhtml >}}

---

{{< rawhtml >}}
<div style="text-align: center; margin: 4rem 0 2rem; padding-top: 2rem; border-top: 1px solid var(--border);">
    <h3 style="margin-bottom: 1rem; color: var(--primary); font-size: 1.5rem;">Looking for Collaboration?</h3>
    <p style="margin-bottom: 2rem; color: var(--secondary); max-width: 600px; margin-inline: auto;">I'm always interested in discussing new opportunities, technical challenges, and innovative projects.</p>
    <div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;">
        <a href="https://github.com/VarunArora14" class="project-link github-link" target="_blank" style="padding: 0.6rem 1.2rem; background: var(--card-bg); border: 1px solid var(--border);">
            GitHub Profile
        </a>
        <a href="https://medium.com/@varunarora1408" class="project-link github-link" target="_blank" style="padding: 0.6rem 1.2rem; background: var(--card-bg); border: 1px solid var(--border);">
            Technical Blog
        </a>
        <a href="https://varunarora14.github.io/" class="project-link github-link" target="_blank" style="padding: 0.6rem 1.2rem; background: var(--card-bg); border: 1px solid var(--border);">
            Full Blog
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
            infoBar.innerHTML = `<span style="font-weight:600">★ ${data.stargazers_count}</span><span>Updated ${new Date(data.pushed_at).toLocaleDateString(undefined,{month:'short',day:'numeric',year:'numeric'})}</span>`;
            const links = card.querySelector('.project-links');
            if(links) links.parentNode.insertBefore(infoBar, links);
        }
    }
    initStats();
})();
</script>
{{< /rawhtml >}}
