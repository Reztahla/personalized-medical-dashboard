# Personalized Medical Dashboard

A conceptual full-stack application that centralizes a patient's medical data in a single, secure dashboard. The goal of the project is to explore how clinicians and patients might collaborate around a personalized health record powered by analytics and clear visualizations.

## Features
- **Unified patient profile:** Aggregate demographics, allergies, medication history, and ongoing treatments into one view using FHIR-compliant resource bundles.
- **Clinical metrics tracking:** Track vitals, lab results, and wearable data through interactive charts fed by streaming observations.
- **Care plan management:** Manage appointments, care team notes, and follow-up tasks with clinician and patient co-authoring workflows.
- **Notifications:** Provide timely alerts for critical values, prescription refills, and upcoming visits across email, SMS, and in-app channels.

## Revised Authorization & Security Strategy
Security is a core focus of this dashboard. The authorization model is centered on role-based access control (RBAC) with data minimization and explicit data segmentation:

1. **Role definitions:**
   - *Patient* – Access to their own health record, ability to share specific data subsets with caregivers, and revoke access at any time.
   - *Clinician* – Access to assigned patient panels, including editing rights for care plans and orders with just-in-time elevation for critical procedures.
   - *Administrator* – System configuration privileges and audit log access without touching clinical content.
2. **Scoped permissions:** Each API endpoint enforces scopes (e.g., `patient.read`, `careplan.write`) that must match a token's claims. Tokens are minted via OAuth 2.1 with short lifetimes, bound to device fingerprints, and refresh tokens are stored using envelope encryption.
3. **Data segmentation:** Sensitive objects such as mental health notes or HIV status require elevated scopes, ensuring least-privilege by default. Break-glass events trigger mandatory session re-authentication.
4. **Attribute checks:** Contextual policies consider consent directives, encounter status, and organization affiliation to prevent lateral data movement.
5. **Audit trails:** Every protected action is logged with actor ID, timestamp, and payload hash for traceability. Audit events stream into SIEM tooling with anomaly detection.

This revised authorization approach can be implemented using libraries such as [Oso](https://www.osohq.com/), [Casbin](https://casbin.org/), or custom middleware layered on top of frameworks like FastAPI, Express, or NestJS.

### Security Hardening Checklist
- Enforce TLS 1.3 and mutual TLS between services.
- Rotate signing keys automatically via the identity provider's JWKS endpoint.
- Adopt security headers (HSTS, CSP, frameguard) and dependency scanning in CI.
- Run regular penetration tests focused on multi-tenant data isolation.

## Architecture Overview
The platform follows a "FHIR-first" integration philosophy:

- **Data ingestion orchestrator:** Event-driven workers normalize data from EHRs, wearables, and labs into FHIR resources before persisting to the clinical data store.
- **Service mesh:** Backend services (patient summary, analytics, notifications) communicate through a service mesh with policy enforcement points for consistent authz decisions.
- **Analytics layer:** A feature store and model orchestration service score patient risk and surface insights in near real time.
- **Frontend shell:** A modular web shell renders widgets registered by micro-frontend teams while honoring the same RBAC and consent rules.

## Getting Started
1. **Clone the repository**
   ```bash
   git clone https://github.com/your-org/personalized-medical-dashboard.git
   ```
2. **Install dependencies** (choose the stack you prefer):
   ```bash
   # Example: install Python backend
   pip install -r requirements.txt

   # Example: install JavaScript frontend
   npm install
   ```
3. **Configure environment variables**
   - `DATABASE_URL` for persistence
   - `AUTH_ISSUER`, `AUTH_CLIENT_ID`, `AUTH_CLIENT_SECRET` for OAuth 2.1 provider
   - `ENCRYPTION_KEY` for protecting refresh tokens
4. **Run the application**
   ```bash
   # Backend
   uvicorn api.main:app --reload

   # Frontend
   npm run dev
   ```

5. **Seed sample data**
   ```bash
   python scripts/seed_fhir_resources.py --patient-count 10
   ```

## Testing
```bash
pytest
npm test
```

### Test data contracts
```bash
python scripts/validate_fhir_contracts.py
```

## Roadmap
- Integrate FHIR data import/export workflows.
- Add patient-facing mobile companion app.
- Build analytics layer with predictive risk scoring.
- Expand authorization policies for research users.

## Contributing
Pull requests are welcome! Please open an issue to discuss major changes before submitting a PR. Include updated threat models and data flow diagrams when modifying authorization pathways.

## License
This project is released under the MIT License. See `LICENSE` for details.
