#  Architecture Technique — Application de Gestion des Conférences & Keynotes

##  Objectif
Cette application permet de **gérer des conférences** et leurs **intervenants (keynotes)**.  
Elle est développée selon une **architecture micro-services** afin de faciliter l’évolutivité, la maintenance et la sécurité.

---

##  Composants principaux

###  Services fonctionnels
- **Keynote Service**
  - Gère les informations des keynotes (id, nom, prénom, email, fonction).
- **Conference Service**
  - Gère les conférences (titre, type, date, durée, nb inscrits, score).
  - Permet d’ajouter et consulter des *reviews* (note et commentaire).

###  Services techniques
- **API Gateway (Spring Cloud Gateway)**  
  - Point d’entrée unique pour toutes les requêtes.
  - Gère la sécurité (vérification du token Keycloak).
- **Discovery Service (Eureka Server)**  
  - Permet l’enregistrement et la découverte automatique des micro-services.
- **Config Server (Spring Cloud Config)**  
  - Centralise les configurations des services.
- **Keycloak**  
  - Fournit l’authentification et l’autorisation (OAuth2 / OIDC).
- **Frontend (Angular)**  
  - Interface utilisateur web connectée à la Gateway.

---

##  Architecture générale

[ Angular Frontend ]
│
▼
[ API Gateway ] ──> [ Keynote Service ] ──> [ DB Keynote ]
│
└────> [ Conference Service ] ──> [ DB Conference + Reviews ]

[ Discovery Service (Eureka) ] ⇆ [ Tous les micro-services ]
[ Config Server ] ⇆ [ Tous les micro-services ]
[ Keycloak ] ⇆ [ Gateway + Frontend ]



---

##  Sécurité

- Authentification et autorisation via **Keycloak** (OAuth2 / OpenID Connect).
- La **Gateway** vérifie le token JWT avant de rediriger les requêtes.
- Les micro-services utilisent le **token propagé** pour sécuriser les appels internes.
- Gestion des rôles :
  - `ROLE_USER` — accès de base.
  - `ROLE_ADMIN` — gestion des conférences et keynotes.

---

##  Documentation et Résilience

- Chaque micro-service expose une documentation REST via **Swagger / OpenAPI** :
  - `http://localhost:808x/swagger-ui.html`
- **Resilience4J** est utilisé pour :
  - Gérer les pannes (circuit breaker).
  - Effectuer des retries automatiques en cas d’erreur.
  - Prévenir la surcharge (bulkhead, rate limiter).

---

##  Technologies utilisées

| Couche | Outils / Frameworks |
|---------|---------------------|
| **Backend** | Java 17+, Spring Boot, Spring Cloud |
| **Frontend** | Angular |
| **Sécurité** | Keycloak, OAuth2, OIDC |
| **Découverte** | Eureka Server |
| **Configuration** | Spring Cloud Config |
| **Gateway** | Spring Cloud Gateway |
| **Base de données** | PostgreSQL |
| **Documentation API** | Swagger / OpenAPI |
| **Résilience** | Resilience4J |

---

##  Déploiement (vue d’ensemble)

1. Lancer **Eureka Server** et **Config Server**.  
2. Démarrer **Keycloak** (realm + clients configurés).  
3. Démarrer les micro-services :  
   - `keynote-service`  
   - `conference-service`  
4. Lancer **API Gateway**.  
5. Démarrer **Frontend Angular** (`npm start`).  

---

## 📁 Structure du projet (exemple)

```bash
conference-app/
│
├── README.md
│
├── config-service/                # Service de configuration (Spring Cloud Config)
│   ├── src/
│   ├── pom.xml
│   └── application.yml
│
├── discovery-service/             # Service de découverte (Eureka Server)
│   ├── src/
│   ├── pom.xml
│   └── application.yml
│
├── gateway-service/               # API Gateway (Spring Cloud Gateway)
│   ├── src/
│   ├── pom.xml
│   └── application.yml
│
├── keynote-service/               # Microservice Keynote (gestion des intervenants)
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/example/keynote/
│   │   │   │   ├── controller/
│   │   │   │   ├── service/
│   │   │   │   ├── entity/
│   │   │   │   ├── repository/
│   │   │   │   └── KeynoteServiceApplication.java
│   │   │   └── resources/
│   │   │       ├── application.yml
│   │   │       └── data.sql
│   ├── pom.xml
│   └── Dockerfile
│
├── conference-service/            # Microservice Conférence + Reviews
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/example/conference/
│   │   │   │   ├── controller/
│   │   │   │   ├── service/
│   │   │   │   ├── entity/
│   │   │   │   ├── repository/
│   │   │   │   ├── client/
│   │   │   │   └── ConferenceServiceApplication.java
│   │   │   └── resources/
│   │   │       └── application.yml
│   ├── pom.xml
│   └── Dockerfile
│
├── frontend-angular/
│   ├── src/
│   │   ├── app/
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   │   ├── keynote.service.ts
│   │   │   │   ├── conference.service.ts
│   │   │   │   └── auth.service.ts
│   │   │   └── app.module.ts
│   ├── angular.json
│   ├── package.json
│   └── tsconfig.json
│
├── keycloak/
│   ├── realm-export.json
│   └── import.sh
│
├── docker-compose.yml
├── k8s/
│   ├── keynote-deployment.yaml
│   ├── conference-deployment.yaml
│   ├── gateway-deployment.yaml
│   ├── eureka-deployment.yaml
│   ├── config-deployment.yaml
│   └── keycloak-deployment.yaml
└── pom.xml



🖋️ *Auteur : Oumayma DAOUDI*  
📅 *Date : 10 Octobre 2025*  