<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
  <title>Hub des Métiers - Esport & Gaming</title>
  <style>
    /* =========================================
       1. RESET & VARIABLES
       ========================================= */
    :root {
        --bg-dark: #080812;
        --text-main: #ffffff;
        --text-muted: #94a3b8;
        --accent-glow: rgba(59, 130, 246, 0.5);
        --theme-color: #3b82f6; /* Default fallback */

        --glass-bg: rgba(20, 20, 35, 0.65);
        --glass-border: rgba(255, 255, 255, 0.08);
        --glass-blur: blur(16px);

        --font-main: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; outline: none; }

    body {
        font-family: var(--font-main);
        background-color: var(--bg-dark);
        color: var(--text-main);
        height: 100vh;
        width: 100vw;
        overflow: hidden;
        /* Background subtil */
        background-image:
            radial-gradient(circle at 15% 50%, rgba(76, 29, 149, 0.08), transparent 25%),
            radial-gradient(circle at 85% 30%, rgba(16, 185, 129, 0.06), transparent 25%);
    }

    /* =========================================
       2. HEADER MINIMALISTE
       ========================================= */
    .header-minimal {
        position: fixed; top: 24px; left: 24px; z-index: 100; pointer-events: none;
    }

    .brand-container {
        pointer-events: auto;
        display: flex; align-items: center; gap: 12px;
        padding: 8px 16px 8px 12px;
        background: rgba(10, 10, 15, 0.6);
        backdrop-filter: blur(8px);
        border: 1px solid rgba(255, 255, 255, 0.05);
        border-radius: 40px;
        transition: transform 0.3s ease;
    }
    .brand-container:hover { transform: scale(1.02); border-color: rgba(255, 255, 255, 0.1); }
    .brand-logo { height: 48px; width: auto; border-radius: 8px; }
    .brand-title {
        font-size: 1.1rem; font-weight: 700; letter-spacing: -0.02em;
        background: linear-gradient(to right, #fff, #cbd5e1);
        -webkit-background-clip: text; -webkit-text-fill-color: transparent;
        text-transform: uppercase;
    }

    /* =========================================
       3. NAVIGATION DOCK (FLOTTANT)
       ========================================= */
    .dock-nav {
        position: fixed; bottom: 32px; left: 50%; transform: translateX(-50%); z-index: 1000;
        display: flex; align-items: center; gap: 8px; padding: 8px;
        background: var(--glass-bg); backdrop-filter: var(--glass-blur);
        border: 1px solid var(--glass-border); border-radius: 24px;
        box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4), 0 0 0 1px rgba(255, 255, 255, 0.05);
        transition: all 0.4s cubic-bezier(0.2, 0.8, 0.2, 1);
    }
    .dock-btn {
        background: transparent; border: none; color: var(--text-muted);
        padding: 10px 20px; border-radius: 16px; cursor: pointer;
        font-family: var(--font-main); font-weight: 600; font-size: 0.95rem;
        display: flex; align-items: center; gap: 8px; transition: all 0.2s ease;
    }
    .dock-icon { font-size: 1.1rem; }
    .dock-btn:hover { color: #fff; background: rgba(255, 255, 255, 0.05); }
    .dock-btn.active { background: rgba(255, 255, 255, 0.1); color: #fff; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2); }
    .dock-divider { width: 1px; height: 24px; background: rgba(255, 255, 255, 0.1); margin: 0 4px; }

    /* =========================================
       4. CONTAINERS VUES
       ========================================= */
    .view-container {
        position: absolute; top: 0; left: 0; width: 100%; height: 100%;
        opacity: 0; pointer-events: none;
        transition: opacity 0.4s ease, transform 0.4s ease;
        transform: scale(0.98);
    }
    .view-container.active-view { opacity: 1; pointer-events: auto; transform: scale(1); z-index: 10; }

    /* =========================================
       5. MAP UI & CANVAS
       ========================================= */
    #canvas { display: block; width: 100%; height: 100%; cursor: grab; }
    #canvas:active { cursor: grabbing; }

    .map-ui-overlay { position: absolute; inset: 0; pointer-events: none; }
    .map-controls {
        position: absolute; top: 24px; right: 24px; display: flex; gap: 8px; pointer-events: auto;
    }
    .control-btn {
        background: rgba(15, 15, 20, 0.6); backdrop-filter: blur(8px);
        border: 1px solid rgba(255, 255, 255, 0.1); color: var(--text-muted);
        padding: 8px 14px; border-radius: 12px; font-size: 0.85rem; font-weight: 600;
        cursor: pointer; transition: all 0.2s;
    }
    .control-btn:hover { background: rgba(255, 255, 255, 0.1); color: #fff; }
    .control-btn.active { background: rgba(255, 255, 255, 0.15); color: #fff; border-color: rgba(255, 255, 255, 0.3); }

    /* TOOLTIP INTELLIGENT */
    .tooltip {
        position: fixed; top: 0; left: 0; width: 300px; pointer-events: none; z-index: 50;
        background: rgba(12, 12, 18, 0.95); backdrop-filter: blur(12px);
        border: 1px solid rgba(255, 255, 255, 0.1); border-radius: 16px;
        padding: 16px; box-shadow: 0 16px 40px rgba(0, 0, 0, 0.6);
        opacity: 0; transform: scale(0.95); transition: opacity 0.2s, transform 0.2s;
    }
    .tooltip.active { opacity: 1; transform: scale(1); }
    .tooltip-header { font-size: 1.1rem; font-weight: 800; margin-bottom: 8px; color: #fff; text-transform: uppercase; }
    .tooltip-badges { display: flex; gap: 8px; margin-bottom: 10px; flex-wrap: wrap; }
    .badge-cat, .badge-salary { font-size: 0.7rem; font-weight: 700; text-transform: uppercase; padding: 3px 8px; border-radius: 6px; }
    .badge-cat { background: rgba(255, 255, 255, 0.1); color: #fff; }
    .badge-salary { background: rgba(16, 185, 129, 0.15); color: #34d399; border: 1px solid rgba(16, 185, 129, 0.2); }
    .tooltip-desc { font-size: 0.85rem; color: #94a3b8; line-height: 1.4; font-style: italic; margin-bottom: 8px; }
    .tooltip-missions { font-size: 0.8rem; color: #cbd5e1; border-top: 1px solid rgba(255, 255, 255, 0.1); padding-top: 8px; }

    /* =========================================
       6. LISTE METIERS UI
       ========================================= */
    #view-list { overflow-y: auto; padding-bottom: 120px; }
    .list-container { max-width: 1200px; margin: 0 auto; padding: 100px 24px 0 24px; }
    .list-hero { text-align: center; margin-bottom: 60px; }
    .list-main-title {
        font-size: 3rem; font-weight: 800; margin-bottom: 12px;
        background: linear-gradient(135deg, #fff 0%, #94a3b8 100%);
        -webkit-background-clip: text; -webkit-text-fill-color: transparent;
    }
    .categories-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 24px; }
    .cat-card {
        background: rgba(255, 255, 255, 0.03); border: 1px solid rgba(255, 255, 255, 0.06);
        border-radius: 20px; padding: 24px; cursor: pointer; position: relative;
        overflow: hidden; height: 180px; display: flex; flex-direction: column; justify-content: space-between;
        transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
    }
    .cat-card:hover {
        transform: translateY(-8px); background: rgba(255, 255, 255, 0.06);
        border-color: var(--theme-color); box-shadow: 0 12px 30px -10px rgba(0, 0, 0, 0.6), 0 0 0 1px var(--theme-color);
    }
    .cat-icon-box { font-size: 2.5rem; margin-bottom: 16px; transition: transform 0.3s; }
    .cat-card:hover .cat-icon-box { transform: scale(1.2) rotate(6deg); }
    .cat-content h3 { font-size: 1.2rem; font-weight: 700; color: #fff; text-transform: uppercase; margin-bottom: 4px; }
    .cat-meta { font-size: 0.85rem; color: var(--text-muted); }
    .cat-card::after { content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 4px; background: var(--theme-color); opacity: 0.6; }

    /* =========================================
       7. MODALE UI
       ========================================= */
    .modal-overlay {
        position: fixed; inset: 0; z-index: 2000; background: rgba(0, 0, 0, 0.8);
        backdrop-filter: blur(8px); display: flex; align-items: center; justify-content: center;
        padding: 20px; opacity: 0; pointer-events: none; transition: opacity 0.3s ease;
    }
    .modal-overlay.active { opacity: 1; pointer-events: auto; }
    .modal-card {
        background: #12121a; width: 100%; max-width: 800px; max-height: 85vh;
        border-radius: 24px; border: 1px solid rgba(255, 255, 255, 0.1);
        box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7); display: flex; flex-direction: column;
        transform: scale(0.96); transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    }
    .modal-overlay.active .modal-card { transform: scale(1); }
    .modal-header { padding: 24px; border-bottom: 1px solid rgba(255, 255, 255, 0.06); display: flex; justify-content: space-between; align-items: center; }
    .modal-header-left { display: flex; align-items: center; gap: 16px; }
    .modal-category-icon { font-size: 2rem; }
    .modal-header h3 { font-size: 1.5rem; font-weight: 800; text-transform: uppercase; color: white; margin: 0; }
    .close-modal-btn { background: none; border: none; color: var(--text-muted); font-size: 1.5rem; cursor: pointer; padding: 8px; }
    .close-modal-btn:hover { color: #fff; }
    .modal-body { padding: 24px; overflow-y: auto; flex: 1; }

    /* Job Items Modal */
    .job-item { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.04); border-radius: 12px; padding: 20px; margin-bottom: 16px; }
    .job-head { display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px; }
    .job-title { font-size: 1.1rem; font-weight: 700; color: #fff; }
    .job-pay { font-size: 0.8rem; font-weight: 700; color: #10b981; background: rgba(16, 185, 129, 0.1); padding: 4px 10px; border-radius: 6px; }
    .job-desc { font-size: 0.95rem; color: #cbd5e1; line-height: 1.5; margin-bottom: 12px; font-style: italic; background: rgba(0, 0, 0, 0.2); padding: 12px; border-radius: 8px; }
    .job-infos { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
    .info-box strong { display: block; font-size: 0.75rem; text-transform: uppercase; color: #64748b; margin-bottom: 4px; }
    .info-box p { font-size: 0.9rem; color: #e2e8f0; }
    .job-learn-more {
        display: inline-block; margin-top: 16px; padding: 8px 16px;
        background: rgba(59, 130, 246, 0.1); color: #60a5fa; text-decoration: none;
        font-size: 0.85rem; font-weight: 600; border-radius: 8px;
        border: 1px solid rgba(59, 130, 246, 0.2); transition: all 0.2s;
    }
    .job-learn-more:hover { background: rgba(59, 130, 246, 0.2); color: #93c5fd; }

    /* RESPONSIVE */
    @media (max-width: 768px) {
        .header-minimal { top: 16px; left: 16px; }
        .brand-container { padding: 6px 12px; }
        .brand-logo { height: 32px; }
        .brand-title { font-size: 0.9rem; }
        .dock-nav { bottom: 20px; width: 90%; justify-content: center; }
        .map-controls { top: 80px; right: 16px; flex-direction: column; align-items: flex-end; }
        .list-container { padding-top: 80px; }
        .job-infos { grid-template-columns: 1fr; }
    }
  </style>
</head>
<body>

  <header class="header-minimal">
    <div class="brand-container">
      <img src="La Plateforme - Logo.jpg" alt="La Plateforme" class="brand-logo">
      <h1 class="brand-title">Career Hub</h1>
    </div>
  </header>

  <div id="view-map" class="view-container active-view">
      <canvas id="canvas"></canvas>
      
      <div class="map-ui-overlay">
          <div class="map-controls">
              <button class="control-btn" onclick="resetView()" title="Recentrer">🎯 Recentrer</button>
              <button class="control-btn active" id="filter-esport" onclick="toggleIndustry('esport')">🏆 Esport</button>
              <button class="control-btn active" id="filter-gamedev" onclick="toggleIndustry('gamedev')">🎮 JV</button>
          </div>

          <div class="tooltip" id="tooltip">
              <div class="tooltip-header" id="tooltipTitle"></div>
              <div class="tooltip-badges">
                  <span class="badge-cat" id="tooltipCategory"></span>
                  <span class="badge-salary" id="tooltipSalary"></span>
              </div>
              <div class="tooltip-desc" id="tooltipDesc"></div>
              <div class="tooltip-missions">
                  <strong>Missions :</strong> <span id="tooltipMissions"></span>
              </div>
          </div>
      </div>
  </div>

  <div id="view-list" class="view-container">
      <div class="list-container">
          <header class="list-hero">
              <h2 class="list-main-title">L'Annuaire</h2>
              <p class="list-subtitle">Explorez les définitions et détails de chaque profession</p>
          </header>
          <div class="categories-grid" id="listGrid"></div>
      </div>
  </div>

  <nav class="dock-nav">
      <button class="dock-btn active" id="btn-map" onclick="switchView('map')">
          <span class="dock-icon">🌍</span>
          <span class="dock-label">Carte</span>
      </button>
      <div class="dock-divider"></div>
      <button class="dock-btn" id="btn-list" onclick="switchView('list')">
          <span class="dock-icon">📋</span>
          <span class="dock-label">Annuaire</span>
      </button>
  </nav>

  <div class="modal-overlay" id="listModal">
      <div class="modal-card">
          <div class="modal-header">
              <div class="modal-header-left">
                  <span class="modal-category-icon" id="modalIcon"></span>
                  <h3 id="modalTitle">Titre Categorie</h3>
              </div>
              <button class="close-modal-btn" onclick="closeListModal()">✕</button>
          </div>
          <div class="modal-body" id="modalBody"></div>
      </div>
  </div>

  <script>
    /* =========================================
       1. DONNÉES ET CONFIGURATION
       ========================================= */
    const categoriesConfig = {
        performance: { color: '#22c55e', label: 'PERFORMANCE', industry: 'esport', icon: '🏆' },
        animation: { color: '#06b6d4', label: 'ANIMATION', industry: 'esport', icon: '🎥' },
        technique_esport: { color: '#eab308', label: 'TECHNIQUE', industry: 'esport', icon: '🔌' },
        marketing: { color: '#ef4444', label: 'MARKETING', industry: 'esport', icon: '📣' },
        communication: { color: '#f97316', label: 'COMMUNICATION', industry: 'esport', icon: '💬' },
        juridique: { color: '#8b5cf6', label: 'JURIDIQUE', industry: 'esport', icon: '⚖️' },
        financier: { color: '#94a3b8', label: 'FINANCIER', industry: 'esport', icon: '💶' },
        business: { color: '#92400e', label: 'BUSINESS', industry: 'esport', icon: '💼' },
        rh: { color: '#ec4899', label: 'RESSOURCES HUMAINES', industry: 'esport', icon: '👥' },
        evenement: { color: '#db2777', label: 'ÉVÉNEMENT', industry: 'esport', icon: '🎉' },
        media: { color: '#1e40af', label: 'MÉDIA', industry: 'esport', icon: '📰' },
        education: { color: '#60a5fa', label: 'ÉDUCATION', industry: 'esport', icon: '🎓' },
        digital: { color: '#0ea5a4', label: 'DIGITAL', industry: 'esport', icon: '🖥️' },

        programmation: { color: '#10b981', label: 'PROGRAMMATION', industry: 'gamedev', icon: '💻' },
        image_arts: { color: '#f59e0b', label: 'IMAGE & ARTS', industry: 'gamedev', icon: '🎨' },
        design_ux: { color: '#8b5cf6', label: 'DESIGN & UX', industry: 'gamedev', icon: '📐' },
        production: { color: '#ef4444', label: 'PRODUCTION', industry: 'gamedev', icon: '📅' },
        marketing_gamedev: { color: '#ec4899', label: 'MARKETING JEU', industry: 'gamedev', icon: '🛍️' },
        support: { color: '#6366f1', label: 'SUPPORT STUDIO', industry: 'gamedev', icon: '🏢' }
    };

    const allData = {
        performance: {
            subcategories: {
                'Joueurs': [
                    { name: 'Joueur de haut niveau', description: 'Athlète du gaming compétitif.', missions: 'Entraînement, Compétition', formation: 'Académie / Autodidacte', salaireDebutant: '24k€', salaireExperience: '150k€+' }
                ],
                'Encadrement': [
                    { name: 'Coach', description: 'Encadre la stratégie et l\'entraînement.', missions: 'Coaching, Analyse', formation: 'Expérience terrain', salaireDebutant: '30k€', salaireExperience: '75k€' },
                    { name: 'Analyste', description: 'Analyse les données de match et performances.', missions: 'Data, VOD review', formation: 'Data / Sport', salaireDebutant: '28k€', salaireExperience: '60k€' },
                    { name: 'Dirigeant de structure', description: 'Gère l\'organisation et la vision globale.', missions: 'Direction, Stratégie', formation: 'Management', salaireDebutant: '45k€', salaireExperience: '100k€' },
                    { name: 'Directeur sportif', description: 'Pilote la partie sportive de la structure.', missions: 'Recrutement, Stratégie', formation: 'Sport Management', salaireDebutant: '40k€', salaireExperience: '90k€' },
                    { name: 'Scout / recruteur', description: 'Repère et recrute les talents.', missions: 'Scouting, Trials', formation: 'Expérience terrain', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Team manager', description: 'Gère la logistique et la vie d\'équipe.', missions: 'Planning, Logistique', formation: 'Management', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Agent / Career Manager', description: 'Gère la carrière et les contrats des joueurs.', missions: 'Négociation, Management', formation: 'Droit / Management', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ],
                'Staff médical & préparation': [
                    { name: 'Médecin', description: 'Suivi médical global des joueurs.', missions: 'Soins, Suivi', formation: 'Médecine', salaireDebutant: '50k€', salaireExperience: '90k€' },
                    { name: 'Kiné', description: 'Rééducation et prévention.', missions: 'Soins, Prévention', formation: 'Kinésithérapie', salaireDebutant: '30k€', salaireExperience: '60k€' },
                    { name: 'Ostéo', description: 'Soins ostéopathiques spécialisés.', missions: 'Soins, Prévention', formation: 'Ostéopathie', salaireDebutant: '30k€', salaireExperience: '60k€' },
                    { name: 'Diététicien / Nutritionniste', description: 'Planification nutritionnelle pour performance.', missions: 'Menus, Conseils', formation: 'Nutrition', salaireDebutant: '28k€', salaireExperience: '50k€' },
                    { name: 'Préparateur physique', description: 'Conditionnement physique des joueurs.', missions: 'Prépa physique, Fitness', formation: 'Sport', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Préparateur mental', description: 'Accompagnement psychologique et mental.', missions: 'Mental coaching', formation: 'Psychologie', salaireDebutant: '35k€', salaireExperience: '65k€' },
                    { name: 'Suivi médical et santé', description: 'Surveillance sanitaire et prévention.', missions: 'Monitoring, Protocoles', formation: 'Santé publique', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        animation: {
            subcategories: {
                'Talents': [
                    { name: 'Streamer', description: 'Diffuse du contenu live pour fédérer une communauté.', missions: 'Live, Interaction', formation: 'Autodidacte', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Caster', description: 'Commente les matchs et apporte de la valeur analytique.', missions: 'Commentaire, Analyse', formation: 'Journalisme / Scène', salaireDebutant: 'Cachets', salaireExperience: 'Variable' },
                    { name: 'Host', description: 'Présente et anime les plateaux.', missions: 'Présentation, Interview', formation: 'TV / Com', salaireDebutant: 'Cachets', salaireExperience: 'Variable' },
                    { name: 'Vidéaste', description: 'Crée du contenu vidéo édité pour les plateformes.', missions: 'Montage, Storytelling', formation: 'Audiovisuel', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Animateur gaming / eSport', description: 'Anime des formats et événements autour du jeu.', missions: 'Animation, Interaction', formation: 'Expérience média', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        technique_esport: {
            subcategories: {
                'Tech & Live': [
                    { name: 'Technicien réseau', description: 'Assure la stabilité réseau en compétition.', missions: 'Réseau, Monitoring', formation: 'Réseau', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Technicien d’exploitation', description: 'Gère les serveurs et l\'infrastructure.', missions: 'Serveurs, Déploiement', formation: 'BTS/Ingénieur', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Ingénieur son', description: 'Conçoit la partie audio du show.', missions: 'Son, Mix', formation: 'Audio', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Ingénieur lumière', description: 'Gère la scénographie lumineuse.', missions: 'Éclairage, Setups', formation: 'Éclairage', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Régisseur', description: 'Coordonne la technique sur le plateau.', missions: 'Régie, Montage', formation: 'Audiovisuel', salaireDebutant: '28k€', salaireExperience: '50k€' },
                    { name: 'Assistant plateau', description: 'Assiste la régie et la production sur le plateau.', missions: 'Support technique', formation: 'Audiovisuel', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Runner', description: 'Réalise des missions terrain et logistiques.', missions: 'Logistique, Support', formation: 'Opérationnel', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        marketing: {
            subcategories: {
                'Marketing global': [
                    { name: 'Chef de produit', description: 'Conçoit et pilote offres et produits.', missions: 'Produit, Go-to-market', formation: 'Marketing', salaireDebutant: '35k€', salaireExperience: '65k€' },
                    { name: 'Chef de publicité', description: 'Pilote les campagnes publicitaires.', missions: 'Media, Achat', formation: 'Marketing', salaireDebutant: '32k€', salaireExperience: '60k€' },
                    { name: 'Trade marketing', description: 'Optimise la distribution et merchandising.', missions: 'Trade, Merch', formation: 'Commerce', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Trade merch', description: 'Gère le merchandising commercial.', missions: 'Merch, Vente', formation: 'Commerce', salaireDebutant: '28k€', salaireExperience: '50k€' },
                    { name: 'Responsable sponsoring', description: 'Manage les partenariats sponsors.', missions: 'Sponsoring, Relations', formation: 'Marketing', salaireDebutant: '35k€', salaireExperience: '80k€' },
                    { name: 'Responsable licensing', description: 'Pilote les licences et droits produits.', missions: 'Licensing, Contrats', formation: 'Marketing/Droit', salaireDebutant: '35k€', salaireExperience: '70k€' },
                    { name: 'Responsable partenariat', description: 'Développe collaborations stratégiques.', missions: 'Partenariat, Négociation', formation: 'Commerce', salaireDebutant: '32k€', salaireExperience: '65k€' },
                    { name: 'Responsable localisation', description: 'Adapte le produit aux marchés locaux.', missions: 'Localisation, QA', formation: 'Langues/Prod', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Brand manager', description: 'Pilote l\'identité et stratégie de marque.', missions: 'Branding, Stratégie', formation: 'Marketing', salaireDebutant: '35k€', salaireExperience: '65k€' }
                ]
            }
        },
        communication: {
            subcategories: {
                'Comms': [
                    { name: 'Attaché de presse', description: 'Gère les relations presse.', missions: 'RP, Communiqués', formation: 'Com / Journalisme', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Chargé de RP', description: 'Anime les relations publiques et médias.', missions: 'RP, Relations', formation: 'Com', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Chargé de communication interne', description: 'Coordonne la communication interne.', missions: 'Interne, RH', formation: 'Com', salaireDebutant: '28k€', salaireExperience: '50k€' },
                    { name: 'Chargé de communication externe', description: 'Pilote la communication institutionnelle.', missions: 'Externe, RP', formation: 'Com', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Communication corporate', description: 'Gère l\'image institutionnelle.', missions: 'Stratégie, RP', formation: 'Com / MBA', salaireDebutant: '35k€', salaireExperience: '70k€' },
                    { name: 'Rédacteur', description: 'Produit du contenu écrit et éditorial.', missions: 'Rédaction, SEO', formation: 'Lettres / Journalisme', salaireDebutant: '26k€', salaireExperience: '50k€' }
                ]
            }
        },
        juridique: {
            subcategories: {
                'Légal': [
                    { name: 'Juriste', description: 'Conseil juridique interne.', missions: 'Contrats, Droit', formation: 'Master Droit', salaireDebutant: '40k€', salaireExperience: '90k€' },
                    { name: 'Avocat', description: 'Défend et conseille légalement.', missions: 'Contentieux, Contrats', formation: 'Barreau', salaireDebutant: '50k€', salaireExperience: '120k€' },
                    { name: 'Manager juridique', description: 'Pilote la stratégie juridique.', missions: 'Management, Droit', formation: 'Droit + Expérience', salaireDebutant: '50k€', salaireExperience: '110k€' }
                ]
            }
        },
        financier: {
            subcategories: {
                'Finance': [
                    { name: 'Directeur financier', description: 'Supervise les finances de la structure.', missions: 'Finance, Reporting', formation: 'Finance', salaireDebutant: '50k€', salaireExperience: '110k€' },
                    { name: 'Responsable budget', description: 'Gère les budgets opérationnels.', missions: 'Budget, Prévisions', formation: 'Finance', salaireDebutant: '40k€', salaireExperience: '75k€' },
                    { name: 'Comptable / Trésorier', description: 'Gère la comptabilité et trésorerie.', missions: 'Comptabilité, Trésorerie', formation: 'BTS CG/Compta', salaireDebutant: '28k€', salaireExperience: '45k€' }
                ]
            }
        },
        rh: {
            subcategories: {
                'RH': [
                    { name: 'Manager RH', description: 'Gère les stratégies RH.', missions: 'Recrutement, Formation', formation: 'Master RH', salaireDebutant: '40k€', salaireExperience: '70k€' },
                    { name: 'Chargé de recrutement', description: 'Conduit les processus d\'embauche.', missions: 'Sourcing, Interviews', formation: 'RH', salaireDebutant: '28k€', salaireExperience: '45k€' },
                    { name: 'Responsable formation', description: 'Pilote les parcours de formation.', missions: 'Formation, Skilldev', formation: 'Pédagogie', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Responsable mobilité', description: 'Gère la mobilité interne/externe.', missions: 'Mobilité, Carrières', formation: 'RH', salaireDebutant: '30k€', salaireExperience: '55k€' }
                ]
            }
        },
        business: {
            subcategories: {
                'Commerce': [
                    { name: 'Business developer', description: 'Développe le chiffre d\'affaires.', missions: 'Prospection, Deals', formation: 'Commerce', salaireDebutant: '35k€', salaireExperience: '80k€' },
                    { name: 'Commercial', description: 'Vente et relation client.', missions: 'Vente, Négociation', formation: 'Commerce', salaireDebutant: '28k€', salaireExperience: '60k€' },
                    { name: 'Responsable d’accueil', description: 'Gère l’accueil et les relations publiques.', missions: 'Accueil, Support', formation: 'Accueil/Relation', salaireDebutant: '22k€', salaireExperience: '35k€' },
                    { name: 'Acquéreur de droits', description: 'Négocie l\'achat de droits et contenus.', missions: 'Achat droits, Négociation', formation: 'Droit/Commerce', salaireDebutant: '32k€', salaireExperience: '65k€' },
                    { name: 'Revendeur de droits', description: 'Commercialise des droits à des diffuseurs.', missions: 'Vente droits', formation: 'Commerce', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Annonceur', description: 'Pilotage des campagnes sponsorisées.', missions: 'Annonce, Media', formation: 'Marketing', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Équipementier', description: 'Fournit le matériel et partenariats.', missions: 'Fourniture, Partenariat', formation: 'Commerce', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        digital: {
            subcategories: {
                'Digital & Créa': [
                    { name: 'Community manager', description: 'Anime les communautés en ligne.', missions: 'Social, Modération', formation: 'Com', salaireDebutant: '26k€', salaireExperience: '45k€' },
                    { name: 'Social media manager', description: 'Pilote la stratégie réseaux sociaux.', missions: 'Stratégie, Planning', formation: 'Marketing', salaireDebutant: '30k€', salaireExperience: '60k€' },
                    { name: 'Traffic manager', description: 'Optimise l\'acquisition paid/SEO.', missions: 'Ads, SEO', formation: 'Marketing Digital', salaireDebutant: '30k€', salaireExperience: '60k€' },
                    { name: 'Web marketer', description: 'Gère les campagnes digitales.', missions: 'Campagnes, Analytics', formation: 'Marketing', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Web designer', description: 'Crée les interfaces web.', missions: 'UI, Prototypage', formation: 'Design', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Directeur artistique', description: 'Pilote la direction créative digitale.', missions: 'Direction créa', formation: 'Art/Design', salaireDebutant: '35k€', salaireExperience: '80k€' },
                    { name: 'Développeur web', description: 'Développe et maintient les sites.', missions: 'Front/Back', formation: 'Dev', salaireDebutant: '32k€', salaireExperience: '65k€' },
                    { name: 'Développeur mobile', description: 'Crée des applications mobiles.', missions: 'Apps, Backend', formation: 'Dev', salaireDebutant: '32k€', salaireExperience: '65k€' },
                    { name: 'Graphiste', description: 'Réalise les visuels 2D.', missions: 'Créa, Visuel', formation: 'Design', salaireDebutant: '26k€', salaireExperience: '50k€' },
                    { name: 'Graphiste 3D', description: 'Crée des assets 3D pour médias et promos.', missions: '3D, Texturing', formation: '3D School', salaireDebutant: '30k€', salaireExperience: '55k€' }
                ]
            }
        },
        evenement: {
            subcategories: {
                'Opérations': [
                    { name: 'Chef de projet événementiel', description: 'Pilote l\'organisation d\'événements.', missions: 'Coordo, Logistique', formation: 'Event', salaireDebutant: '32k€', salaireExperience: '55k€' },
                    { name: 'Directeur tournoi', description: 'Dirige la tenue du tournoi.', missions: 'Pilotage, Règles', formation: 'Expérience événement', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Admin / Arbitre', description: 'Gère les règles et l\'arbitrage.', missions: 'Arbitrage, Gestion', formation: 'Spécialisée', salaireDebutant: '28k€', salaireExperience: '45k€' },
                    { name: 'Observateur', description: 'Supervise les matchs pour intégrité.', missions: 'Supervision, Reporting', formation: 'Expérience jeu', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Traducteur', description: 'Assure l\'accessibilité linguistique.', missions: 'Traduction, Localisation', formation: 'Langues', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        media: {
            subcategories: {
                'Journalisme & rédaction': [
                    { name: 'Journaliste', description: 'Couvre l\'actualité et rédige des interviews.', missions: 'Rédaction, Enquête', formation: 'Journalisme', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'Secrétaire de rédaction', description: 'Coordonne la production éditoriale.', missions: 'Coordo, Relecture', formation: 'Journalisme', salaireDebutant: '26k€', salaireExperience: '45k€' },
                    { name: 'Responsable éditorial', description: 'Pilote la ligne éditoriale.', missions: 'Stratégie édito', formation: 'Journalisme/Com', salaireDebutant: '32k€', salaireExperience: '65k€' }
                ],
                'Audiovisuel & diffusion': [
                    { name: 'Photographe', description: 'Capture visuels pour événements et contenus.', missions: 'Photo, Retouche', formation: 'Photo', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Cadreur', description: 'Filme les actions et interviews.', missions: 'Caméra, Opération', formation: 'Audiovisuel', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Réalisateur', description: 'Conçoit la mise en scène des directs.', missions: 'Réalisation, Coordination', formation: 'Audiovisuel', salaireDebutant: '35k€', salaireExperience: '70k€' },
                    { name: 'Monteur', description: 'Assemble les séquences vidéo.', missions: 'Montage, Post-prod', formation: 'Audiovisuel', salaireDebutant: '26k€', salaireExperience: '50k€' },
                    { name: 'Titreur', description: 'Génère habillages et sous-titres.', missions: 'Graphisme, Titres', formation: 'Audiovisuel/Graphisme', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'TV manager', description: 'Supervise la chaîne et les flux live.', missions: 'Gestion flux', formation: 'TV/Prod', salaireDebutant: '35k€', salaireExperience: '75k€' },
                    { name: 'TV live producer', description: 'Produit les directs télé/stream.', missions: 'Production live', formation: 'Prod', salaireDebutant: '35k€', salaireExperience: '75k€' },
                    { name: 'Diffuseur', description: 'Diffuse les contenus vers les plateformes.', missions: 'Distribution', formation: 'Media', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Directeur de stream', description: 'Pilote la stratégie stream et contenu.', missions: 'Stratégie, Opérations', formation: 'Prod/Media', salaireDebutant: 'Variable', salaireExperience: 'Variable' },
                    { name: 'Directeur de production vidéo', description: 'Supervise la production vidéo.', missions: 'Management, Planning', formation: 'Prod', salaireDebutant: '40k€', salaireExperience: '90k€' },
                    { name: 'Gestionnaire VOD / stream', description: 'Gère les archives et VOD.', missions: 'Maintenance, Upload', formation: 'Media', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        education: {
            subcategories: {
                'Pédagogie & Recherche': [
                    { name: 'Responsable Pédagogique', description: 'Conçoit et coordonne les programmes de formation Esport.', missions: 'Programmes, Suivi élèves', formation: 'Sciences de l\'éducation', salaireDebutant: '32k€', salaireExperience: '55k€' },
                    { name: 'Formateur Esport', description: 'Enseigne la pratique et la théorie de l\'Esport.', missions: 'Cours, Coaching', formation: 'Expérience Pro / Pédagogie', salaireDebutant: '28k€', salaireExperience: '45k€' },
                    { name: 'Directeur d’école Esport', description: 'Dirige un établissement de formation spécialisé.', missions: 'Direction, Stratégie', formation: 'Management / Éducation', salaireDebutant: '45k€', salaireExperience: '80k€' },
                    { name: 'Chercheur en Esport', description: 'Étudie l\'impact sociologique, économique ou cognitif du gaming.', missions: 'Recherche, Publications', formation: 'Doctorat', salaireDebutant: '30k€', salaireExperience: '60k€' },
                    { name: 'Intervenant spécialisé', description: 'Expert ponctuant la formation (droit, business, etc.).', missions: 'Masterclass, Ateliers', formation: 'Expertise Métier', salaireDebutant: 'Variable', salaireExperience: 'Variable' }
                ]
            }
        },
        programmation: {
            subcategories: {
                'Code': [
                    { name: 'Gameplay Programmer', description: 'Le codeur de fun. Il programme les règles du jeu, les mouvements du personnage et tout ce avec quoi le joueur interagit.', missions: 'C++, C#, Gameplay', formation: 'Ingénieur Info', salaireDebutant: '32k€', salaireExperience: '60k€' },
                    { name: 'Engine Programmer', description: 'Le mécanicien du moteur. Il optimise le cœur technologique du jeu pour qu\'il soit beau et fluide.', missions: 'Moteur, Optimisation', formation: 'Ingénieur Info', salaireDebutant: '35k€', salaireExperience: '70k€' },
                    { name: 'AI Programmer', description: 'Le créateur de vie. Il code l\'intelligence des ennemis et des PNJ pour qu\'ils réagissent de manière crédible.', missions: 'IA, Comportement', formation: 'Master IA', salaireDebutant: '38k€', salaireExperience: '75k€' }
                ]
            }
        },
        image_arts: {
            subcategories: {
                'Art': [
                    { name: 'Concept Artist', description: 'Le visionnaire visuel. Il dessine les premières ébauches des personnages et décors pour définir le style du jeu.', missions: 'Dessin, Concept', formation: 'École Art', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: '3D Artist', description: 'Le sculpteur numérique. Il transforme les dessins en modèles 3D texturés prêts à être intégrés dans le jeu.', missions: 'Modélisation, Texture', formation: 'École 3D', salaireDebutant: '30k€', salaireExperience: '55k€' },
                    { name: 'Animator', description: 'Le marionnettiste. Il crée les squelettes et les mouvements des personnages pour leur donner vie.', missions: 'Animation, Rigging', formation: 'Gobelins / Anim', salaireDebutant: '30k€', salaireExperience: '58k€' }
                ]
            }
        },
        design_ux: {
            subcategories: {
                'Design': [
                    { name: 'Game Designer', description: 'L\'architecte ludique. Il conçoit les règles, les mécaniques et la difficulté pour rendre le jeu amusant.', missions: 'Systèmes, Règles', formation: 'Game Design', salaireDebutant: '30k€', salaireExperience: '60k€' },
                    { name: 'Level Designer', description: 'L\'architecte d\'espace. Il construit les niveaux, place les obstacles et guide la progression du joueur.', missions: 'Mapping, Rythme', formation: 'Game Design', salaireDebutant: '28k€', salaireExperience: '55k€' },
                    { name: 'UX Designer', description: 'L\'expert ergonomie. Il s\'assure que les menus sont clairs et que l\'expérience utilisateur est fluide et intuitive.', missions: 'Interface, Ergonomie', formation: 'Master UX', salaireDebutant: '32k€', salaireExperience: '60k€' }
                ]
            }
        },
        production: {
            subcategories: {
                'Prod': [
                    { name: 'Producer', description: 'Le chef de chantier. Il gère le planning, le budget et s\'assure que l\'équipe livre le jeu à temps et sans crunch.', missions: 'Planning, Management', formation: 'Management JV', salaireDebutant: '35k€', salaireExperience: '70k€' },
                    { name: 'QA Tester', description: 'Le chasseur de bugs. Il teste le jeu sous toutes ses coutures pour identifier et rapporter les problèmes techniques.', missions: 'Tests, Rapports', formation: 'Certification', salaireDebutant: '24k€', salaireExperience: '40k€' }
                ]
            }
        },
        marketing_gamedev: {
            subcategories: {
                'Mkt': [
                    { name: 'Brand Manager', description: 'Le gardien de la marque. Il définit l\'identité du jeu et pilote la stratégie marketing mondiale.', missions: 'Stratégie marque', formation: 'École Commerce', salaireDebutant: '35k€', salaireExperience: '65k€' },
                    { name: 'Community Manager', description: 'Le lien studio-joueurs. Il anime la communauté sur les forums et réseaux sociaux et remonte les feedbacks.', missions: 'Animation, Feedback', formation: 'Com Digitale', salaireDebutant: '26k€', salaireExperience: '45k€' }
                ]
            }
        },
        support: {
            subcategories: {
                'Support': [
                    { name: 'Data Analyst', description: 'L\'analyste chiffré. Il étudie les données des joueurs pour comprendre leurs comportements et améliorer le jeu.', missions: 'SQL, Stats, KPI', formation: 'Data Science', salaireDebutant: '38k€', salaireExperience: '70k€' }
                ]
            }
        }
    };

    /* =========================================
       2. GESTION DES VUES (Navigation & UI)
       ========================================= */
    const viewMap = document.getElementById('view-map');
    const viewList = document.getElementById('view-list');
    const btnMap = document.getElementById('btn-map');
    const btnList = document.getElementById('btn-list');

    function switchView(viewId) {
        if (viewId === 'map') {
            viewMap.classList.add('active-view');
            viewList.classList.remove('active-view');
            btnMap.classList.add('active');
            btnList.classList.remove('active');
            requestAnimationFrame(renderMap); 
        } else {
            viewMap.classList.remove('active-view');
            viewList.classList.add('active-view');
            btnMap.classList.remove('active');
            btnList.classList.add('active');
        }
    }

    /* =========================================
       3. CONFIGURATION CANVAS & VARIABLES
       ========================================= */
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');

    // State Variables
    let width, height;
    let scale = 1, offsetX = 0, offsetY = 0;
    let isDragging = false, dragStartX, dragStartY;
    let nodes = [];
    let activeIndustries = { esport: true, gamedev: true };
    let showLines = true;
    let expandedCategories = new Set();
    let hoveredNode = null;

    // Responsive Config
    let config = {
        centerR: 90,
        orbit1R: 350, // Category orbit radius
        orbit2R: 200, // Job orbit radius
        catNodeR: 60,
        jobNodeR: 16,
        isMobile: false
    };

    function updateConfig() {
        width = window.innerWidth;
        height = window.innerHeight;
        canvas.width = width;
        canvas.height = height;

        config.isMobile = width <= 768;

        if (config.isMobile) {
            config.centerR = 60;
            config.orbit1R = 160;
            config.orbit2R = 110;
            config.catNodeR = 40;
            config.jobNodeR = 14;
            scale = Math.max(scale, 0.4); 
        } else {
            config.centerR = 90;
            config.orbit1R = 320;
            config.orbit2R = 180;
            config.catNodeR = 55;
            config.jobNodeR = 16;
        }
    }

    function resizeCanvas() {
        updateConfig();
        initMapNodes();
        renderMap();
    }
    window.addEventListener('resize', resizeCanvas);

    /* =========================================
       4. ALGORITHME "SMART SATELLITES" (Anti-Chevauchement)
       ========================================= */
    function initMapNodes() {
        nodes = [];
        const cx = 0, cy = 0;

        // --- 1. CONFIGURATION DES TAILLES & MARGES ---
        const isMobile = config.isMobile;
        const centerR = config.centerR;
        const catR = config.catNodeR; 

        // Rayon physique (avec marge de sécurité) pour les calculs de collision
        const jobVisR = config.jobNodeR;
        const jobPhysicalSize = (jobVisR * 2) + (isMobile ? 10 : 15); 

        // --- 2. FILTRAGE ---
        const activeKeys = Object.keys(categoriesConfig).filter(k => activeIndustries[categoriesConfig[k].industry]);
        if (activeKeys.length === 0) return;

        // --- 3. CALCUL DU RAYON ORBITAL PRINCIPAL ---
        const minCircumference = activeKeys.length * (catR * 2.5);
        const calculatedRadius = minCircumference / (2 * Math.PI);
        // On prend le max entre le rayon par défaut et le rayon calculé
        const orbit1R = Math.max(isMobile ? 180 : 300, calculatedRadius + 40);

        // --- 4. CALCUL DES POIDS (SYSTÈME ACCORDÉON) ---
        let totalWeight = 0;
        const weights = [];

        activeKeys.forEach(key => {
            let weight = 1;
            if (expandedCategories.has(key) && allData[key]) {
                const count = Object.values(allData[key].subcategories).flat().length;
                weight = 2 + (Math.sqrt(count) * 0.8);
            }
            weights.push({ key, weight });
            totalWeight += weight;
        });

        // --- 5. PLACEMENT DES NODES ---
        // 5A. Node Centrale
        let centerLabel = 'AUCUN\nMÉTIER';
        if (activeIndustries.esport && activeIndustries.gamedev) { centerLabel = 'MÉTIERS\nGAMING & ESPORT'; }
        else if (activeIndustries.esport) { centerLabel = 'MÉTIERS\nESPORT'; }
        else if (activeIndustries.gamedev) { centerLabel = 'MÉTIERS\nJEU VIDÉO'; }

        nodes.push({ id: 'center', type: 'center', x: cx, y: cy, radius: centerR, label: centerLabel, color: '#fff' });

        let currentAngle = -Math.PI / 2; // Départ à midi

        weights.forEach(item => {
            const key = item.key;
            const conf = categoriesConfig[key];

            // Part de camembert allouée (Wedge)
            const wedgeAngle = (item.weight / totalWeight) * 2 * Math.PI;
            // L'angle du centre de la catégorie
            const catAngle = currentAngle + (wedgeAngle / 2);

            const catX = Math.cos(catAngle) * orbit1R;
            const catY = Math.sin(catAngle) * orbit1R;

            // Ajout Node Catégorie
            nodes.push({
                id: key, type: 'category', x: catX, y: catY,
                radius: catR, label: conf.label, icon: conf.icon, color: conf.color,
                industry: conf.industry
            });

            // 5B. PLACEMENT DES MÉTIERS (SATELLITES INTELLIGENTS)
            if (expandedCategories.has(key) && allData[key]) {
                const jobsFlat = Object.values(allData[key].subcategories).flat();
                const totalJobs = jobsFlat.length;

                if (totalJobs > 0) {
                    // Zone de sécurité : on utilise 85% de l'angle alloué
                    const safeWedge = wedgeAngle * 0.85;
                    
                    // Distance de départ
                    let currentOrbitDist = orbit1R + catR + jobVisR + 20;
                    let jobsPlaced = 0;

                    // Boucle pour créer des rangées concentriques
                    while (jobsPlaced < totalJobs) {
                        // Combien de pixels d'arc on a à cette distance ?
                        const arcLengthPixels = currentOrbitDist * safeWedge;
                        
                        // Combien de jobs rentrent physiquement ?
                        let capacity = Math.floor(arcLengthPixels / jobPhysicalSize);
                        if (capacity < 1) capacity = 1;

                        // Combien on en met sur cette ligne ?
                        const countForRow = Math.min(capacity, totalJobs - jobsPlaced);
                        
                        // Calcul des angles pour cette ligne
                        const anglePerJob = safeWedge / countForRow;
                        // Centrage
                        const startRowAngle = catAngle - ((countForRow - 1) * anglePerJob) / 2;

                        for (let i = 0; i < countForRow; i++) {
                            const jobIdx = jobsPlaced + i;
                            const job = jobsFlat[jobIdx];
                            
                            const theta = startRowAngle + (i * anglePerJob);
                            const jx = Math.cos(theta) * currentOrbitDist;
                            const jy = Math.sin(theta) * currentOrbitDist;

                            nodes.push({
                                id: `${key}-job-${jobIdx}`, type: 'job', parentId: key,
                                x: jx, y: jy, radius: jobVisR,
                                label: job.name, color: conf.color, data: job
                            });
                        }
                        // Mise à jour pour la prochaine boucle
                        jobsPlaced += countForRow;
                        currentOrbitDist += (jobVisR * 2) + 12; // On s'éloigne
                    }
                }
            }
            // Avancer l'angle
            currentAngle += wedgeAngle;
        });
        renderMap();
    }

    /* =========================================
       5. RENDU (RENDER MAP)
       ========================================= */
    function renderMap() {
        ctx.setTransform(1, 0, 0, 1, 0, 0);
        ctx.clearRect(0, 0, width, height);

        const centerX = width / 2 + offsetX;
        const centerY = height / 2 + offsetY;

        ctx.translate(centerX, centerY);
        ctx.scale(scale, scale);

        // DRAW CONNECTIONS
        if (showLines) {
            nodes.filter(n => n.type === 'category').forEach(cat => {
                ctx.beginPath(); ctx.moveTo(0, 0); ctx.lineTo(cat.x, cat.y);
                ctx.strokeStyle = 'rgba(255,255,255,0.08)'; ctx.lineWidth = 1; ctx.stroke();
                if (expandedCategories.has(cat.id)) {
                    nodes.filter(n => n.parentId === cat.id).forEach(job => {
                        ctx.beginPath(); ctx.moveTo(cat.x, cat.y); ctx.lineTo(job.x, job.y);
                        ctx.strokeStyle = `${cat.color}44`; ctx.lineWidth = 1; ctx.stroke();
                    });
                }
            });
        }

        // DRAW NODES
        nodes.forEach(n => {
            const isHover = hoveredNode === n;
            let r = n.radius;
            if (isHover) r *= 1.15;

            ctx.beginPath(); ctx.arc(n.x, n.y, r, 0, Math.PI * 2);

            if (n.type === 'center') {
                const grad = ctx.createRadialGradient(n.x, n.y, r * 0.2, n.x, n.y, r);
                grad.addColorStop(0, '#fff'); grad.addColorStop(1, '#e2e8f0');
                ctx.fillStyle = grad; ctx.shadowColor = 'rgba(255,255,255,0.4)'; ctx.shadowBlur = 20;
            } else if (n.type === 'category') {
                ctx.fillStyle = n.color; ctx.shadowBlur = isHover ? 20 : 0; ctx.shadowColor = n.color;
            } else {
                ctx.fillStyle = isHover ? '#fff' : n.color; ctx.shadowBlur = isHover ? 10 : 0; ctx.shadowColor = '#fff';
            }

            ctx.fill(); ctx.shadowBlur = 0;

            if (n.type === 'category') {
                ctx.beginPath(); ctx.arc(n.x, n.y, r + 4, 0, Math.PI * 2);
                ctx.strokeStyle = `${n.color}66`; ctx.lineWidth = 2; ctx.stroke();
            }

            ctx.textAlign = 'center'; ctx.textBaseline = 'middle';

            if (n.type === 'center') {
                ctx.fillStyle = '#1e293b'; ctx.font = 'bold 14px "Inter", sans-serif';
                const lines = n.label.split('\n');
                lines.forEach((l, i) => ctx.fillText(l, n.x, n.y - 6 + (i * 16)));
            } else if (n.type === 'category') {
                ctx.font = `${isHover ? '32px' : '28px'} "Segoe UI Emoji"`;
                ctx.fillText(n.icon, n.x, n.y - 6);
                ctx.fillStyle = '#fff'; ctx.font = `700 ${config.isMobile ? '10px' : '12px'} system-ui`;
                ctx.fillText(n.label, n.x, n.y + 24);
            } else if (n.type === 'job') {
                ctx.fillStyle = isHover ? '#fff' : '#cbd5e1';
                ctx.font = `500 ${config.isMobile ? '9px' : '10px'} system-ui`;
                ctx.fillText(n.label, n.x, n.y + r + 10);
            }
        });
    }

    /* =========================================
       6. INTERACTIONS
       ========================================= */
    function getMousePos(e) {
        const rect = canvas.getBoundingClientRect();
        const cx = width / 2 + offsetX;
        const cy = height / 2 + offsetY;
        const mx = (e.clientX - rect.left - cx) / scale;
        const my = (e.clientY - rect.top - cy) / scale;
        return { x: mx, y: my };
    }

    canvas.addEventListener('mousemove', e => {
        if (isDragging) {
            offsetX = e.clientX - dragStartX; offsetY = e.clientY - dragStartY;
            const limitX = width * 1.5; const limitY = height * 1.5;
            offsetX = Math.max(-limitX, Math.min(limitX, offsetX));
            offsetY = Math.max(-limitY, Math.min(limitY, offsetY));
            renderMap(); return;
        }
        const m = getMousePos(e);
        const n = nodes.find(node => Math.hypot(node.x - m.x, node.y - m.y) < node.radius + 5);

        if (n !== hoveredNode) {
            hoveredNode = n;
            canvas.style.cursor = n ? 'pointer' : 'grab';
            const tt = document.getElementById('tooltip');
            if (n && n.type === 'job') { applyTooltip(n, e.clientX, e.clientY); }
            else { tt.classList.remove('active'); }
            requestAnimationFrame(renderMap);
        } else if (n && n.type === 'job') {
            updateTooltipPos(e.clientX, e.clientY);
        }
    });

    canvas.addEventListener('click', e => {
        if (!hoveredNode) return;
        if (hoveredNode.type === 'category') {
            if (expandedCategories.has(hoveredNode.id)) { expandedCategories.delete(hoveredNode.id); }
            else { expandedCategories.add(hoveredNode.id); }
            initMapNodes(); renderMap();
        }
    });

    canvas.addEventListener('mousedown', e => {
        isDragging = true; dragStartX = e.clientX - offsetX; dragStartY = e.clientY - offsetY;
        canvas.style.cursor = 'grabbing';
    });

    window.addEventListener('mouseup', () => {
        isDragging = false; canvas.style.cursor = hoveredNode ? 'pointer' : 'grab';
    });

    canvas.addEventListener('wheel', e => {
        e.preventDefault();
        const delta = e.deltaY > 0 ? -0.1 : 0.1;
        scale = Math.min(Math.max(0.3, scale + delta), 4);
        renderMap();
    }, { passive: false });

    /* =========================================
       7. TOOLTIP LOGIC
       ========================================= */
    const tooltip = document.getElementById('tooltip');

    function applyTooltip(node, mouseX, mouseY) {
        const d = node.data;
        const conf = categoriesConfig[node.parentId];

        document.getElementById('tooltipTitle').innerText = d.name;
        document.getElementById('tooltipDesc').innerText = `"${d.description}"`;
        document.getElementById('tooltipMissions').innerText = d.missions;
        document.querySelector('.tooltip-header').style.color = conf.color;

        const bCat = document.getElementById('tooltipCategory');
        bCat.innerText = conf.label; bCat.style.backgroundColor = `${conf.color}33`; bCat.style.color = conf.color;

        const bSal = document.getElementById('tooltipSalary');
        if (d.salaireDebutant) { bSal.innerText = `${d.salaireDebutant} /an`; bSal.style.display = 'inline-block'; }
        else { bSal.style.display = 'none'; }

        tooltip.classList.add('active'); updateTooltipPos(mouseX, mouseY);
    }

    function updateTooltipPos(x, y) {
        let finalX = x + 20; let finalY = y + 20;
        if (finalX + 280 > window.innerWidth) finalX = x - 300;
        if (finalY + 200 > window.innerHeight) finalY = y - 220;
        tooltip.style.transform = `translate(${finalX}px, ${finalY}px)`;
    }

    /* =========================================
       8. LISTE & MODALE
       ========================================= */
    function renderList() {
        const grid = document.getElementById('listGrid'); grid.innerHTML = '';
        Object.keys(categoriesConfig).forEach(key => {
            const conf = categoriesConfig[key];
            const data = allData[key];
            let count = 0; if (data) Object.values(data.subcategories).forEach(arr => count += arr.length);

            const card = document.createElement('div'); card.className = 'cat-card';
            card.style.setProperty('--theme-color', conf.color);
            card.onclick = () => openModal(key);
            card.innerHTML = `
                <div class="cat-content">
                    <div class="cat-icon-box" style="color:${conf.color}">${conf.icon}</div>
                    <h3>${conf.label}</h3>
                    <div class="cat-meta">${count} professions</div>
                </div>
                <div class="card-arrow">➝</div>
            `;
            grid.appendChild(card);
        });
    }

    const modalOverlay = document.getElementById('listModal');
    function openModal(key) {
        const conf = categoriesConfig[key];
        const data = allData[key];
        document.getElementById('modalTitle').innerText = conf.label;
        document.getElementById('modalIcon').innerText = conf.icon;
        document.getElementById('modalTitle').style.color = conf.color;
        const body = document.getElementById('modalBody'); body.innerHTML = '';

        if (data) {
            Object.entries(data.subcategories).forEach(([subName, jobs]) => {
                const h4 = document.createElement('h4');
                h4.style.cssText = `color: #94a3b8; font-size: 0.9erm; text-transform:uppercase; margin: 20px 0 10px 0; border-bottom:1px dashed #333; padding-bottom:5px;`;
                h4.innerText = subName; body.appendChild(h4);

                jobs.forEach(job => {
                    const el = document.createElement('div'); el.className = 'job-item';
                    el.innerHTML = `
                        <div class="job-head"><span class="job-title">${job.name}</span><span class="job-pay">${job.salaireDebutant}</span></div>
                        <div class="job-desc">${job.description}</div>
                        <div class="job-infos">
                            <div class="info-box"><strong>Missions</strong><p>${job.missions}</p></div>
                            <div class="info-box"><strong>Formation</strong><p>${job.formation}</p></div>
                        </div>
                        ${job.link ? `<a href="${job.link}" target="_blank" class="job-learn-more">En savoir plus ↗</a>` : ''}
                    `;
                    body.appendChild(el);
                });
            });
        }
        modalOverlay.classList.add('active');
    }

    window.closeListModal = function () { modalOverlay.classList.remove('active'); };
    modalOverlay.addEventListener('click', e => { if (e.target === modalOverlay) closeListModal(); });

    /* =========================================
       9. CONTROLES GLOBAUX
       ========================================= */
    window.toggleIndustry = function (ind) {
        activeIndustries[ind] = !activeIndustries[ind];
        const btn = document.getElementById(`filter-${ind}`);
        if (activeIndustries[ind]) btn.classList.add('active'); else btn.classList.remove('active');
        initMapNodes(); renderMap();
    };

    window.resetView = function () { scale = 1; offsetX = 0; offsetY = 0; renderMap(); };

    // INITIALISATION
    updateConfig();
    initMapNodes();
    renderMap();
    renderList();
  </script>
</body>
</html>
