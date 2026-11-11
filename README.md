# Leadertech-
public
leadertech/
├── apps/
│   ├── web/
│   │   ├── app/
│   │   │   ├── page.tsx
│   │   │   ├── services/
│   │   │   │   └── page.tsx
│   │   │   └── demande/
│   │   │       └── page.tsx
│   │   ├── components/
│   │   │   ├── Header.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── RequestForm.tsx
│   │   ├── lib/
│   │   │   └── api.ts
│   │   └── package.json
│   └── mobile/  (à ajouter plus tard)
├── server/
│   ├── src/
│   │   ├── index.ts
│   │   ├── db.ts
│   │   ├── routes/
│   │   │   └── serviceRequests.ts
│   │   ├── controllers/
│   │   │   └── serviceRequestController.ts
│   │   ├── types.ts
│   │   └── utils/
│   │       └── generateReference.ts
│   ├── package.json
│   ├── tsconfig.json
│   └── .env.example
├── database/
│   └── schema.sql
├── docker-compose.yml
├── README.md
└── .gitignore
CREATE TABLE services (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  description TEXT,
  category VARCHAR(50),
  active BOOLEAN DEFAULT true,
  price_hint TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE service_requests (
  id SERIAL PRIMARY KEY,
  reference VARCHAR(50) UNIQUE NOT NULL,
  client_name VARCHAR(100) NOT NULL,
  client_phone VARCHAR(30) NOT NULL,
  preferred_language VARCHAR(30) NOT NULL DEFAULT 'français',
  service_id INT NOT NULL REFERENCES services(id),
  quartier VARCHAR(100),
  address TEXT,
  description TEXT NOT NULL,
  proposed_price VARCHAR(50),
  payment_method VARCHAR(50),
  request_status VARCHAR(30) DEFAULT 'reçue',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE request_files (
  id SERIAL PRIMARY KEY,
  request_id INT NOT NULL REFERENCES service_requests(id) ON DELETE CASCADE,
  file_url TEXT NOT NULL,
  file_type VARCHAR(50),
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE technicians (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  phone VARCHAR(30),
  specialty VARCHAR(100),
  active BOOLEAN DEFAULT true
);

CREATE TABLE assignments (
  id SERIAL PRIMARY KEY,
  request_id INT NOT NULL REFERENCES service_requests(id) ON DELETE CASCADE,
  technician_id INT NOT NULL REFERENCES technicians(id),
  status VARCHAR(30) DEFAULT 'assignée',
  assigned_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE settings (
  key_name VARCHAR(100) PRIMARY KEY,
  value TEXT,
  updated_at TIMESTAMP DEFAULT NOW()
);

INSERT INTO services (name, description, category, active, price_hint)
VALUES
('Impression', 'Impression de documents et supports', 'impression', true, 'Prix à confirmer selon le besoin'),
('Réparation téléphone', 'Réparation et diagnostic de téléphone', 'phone', true, 'Prix à confirmer selon la panne'),
('Réparation télévision', 'Diagnostic et réparation de télévision', 'tv', true, 'Prix à confirmer selon la panne'),
('Dépannage informatique', 'Support informatique et configuration', 'it', true, 'Prix à confirmer selon le besoin'),
('Installation antenne parabolique', 'Installation et réglage d''antenne', 'antenne', true, 'Prix à confirmer selon l''installation'),
('Installation Starlink', 'Installation et mise en service Starlink', 'starlink', true, 'Prix à confirmer selon l''installation'),
('Livraison', 'Livraison de colis ou matériels', 'delivery', true, 'Frais à convenir selon la destination');
import { Pool } from 'pg';
import dotenv from 'dotenv';

dotenv.config();

export const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production' ? { rejectUnauthorized: false } : false,
});export type ServiceRequestInput = {
  clientName: string;
  clientPhone: string;
  preferredLanguage: string;
  serviceId: number;
  quartier?: string;
  address?: string;
  description: string;
  proposedPrice?: string;
  paymentMethod?: string;
};
