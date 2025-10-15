# Personalized Medical Dashboard

A conceptual full-stack application that centralizes a patient's medical data in a single, secure dashboard. The goal of the project is to explore how clinicians and patients might collaborate around a personalized health record powered by analytics and clear visualizations.

## Features
- **Unified patient profile:** Aggregate demographics, allergies, medication history, and ongoing treatments into one view.
- **Clinical metrics tracking:** Track vitals, lab results, and wearable data through interactive charts.
- **Care plan management:** Manage appointments, care team notes, and follow-up tasks.
- **Notifications:** Provide timely alerts for critical values, prescription refills, and upcoming visits.

## Revised Authorization Strategy
Security is a core focus of this dashboard. The authorization model is centered on role-based access control (RBAC) with data minimization:

1. **Role definitions:**
   - *Patient* – Access to their own health record, ability to share specific data subsets with caregivers.
   - *Clinician* – Access to assigned patient panels, including editing rights for care plans and orders.
   - *Administrator* – System configuration privileges and audit log access without touching clinical content.
2. **Scoped permissions:** Each API endpoint enforces scopes (e.g., `patient.read`, `careplan.write`) that must match a token's claims. Tokens are minted via OAuth 2.1 with short lifetimes and refresh tokens are stored using envelope encryption.
3. **Data segmentation:** Sensitive objects such as mental health notes or HIV status require elevated scopes, ensuring least-privilege by default.
4. **Audit trails:** Every protected action is logged with actor ID, timestamp, and payload hash for traceability.

This revised authorization approach can be implemented using libraries such as [Oso](https://www.osohq.com/), [Casbin](https://casbin.org/), or custom middleware layered on top of frameworks like FastAPI, Express, or NestJS.

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

## Testing
```bash
pytest
npm test
```

## Roadmap
- Integrate FHIR data import/export workflows.
- Add patient-facing mobile companion app.
- Build analytics layer with predictive risk scoring.
- Expand authorization policies for research users.

## Contributing
Pull requests are welcome! Please open an issue to discuss major changes before submitting a PR.

## License
This project is released under the MIT License. See `LICENSE` for details.
