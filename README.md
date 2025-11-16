# AI-Based Grievance Lodging and Tracking System

A comprehensive Flask-based web application for managing citizen grievances with AI-powered department classification, role-based access control, and REST APIs.

## Features

- **Role-Based Access Control**: Three-tier system (Citizen, Officer, Admin)
- **AI-Powered Triage**: Automatic department classification using ML (TF-IDF + Logistic Regression) with keyword-based fallback
- **Grievance Lifecycle Management**: Created → Submitted → Under Review → In Progress → Resolved → Closed
- **REST API**: Versioned endpoints (/api/v1/) for mobile/frontend integration
- **Bootstrap UI**: Responsive, mobile-friendly interface
- **SQLite Database**: Lightweight, no external dependencies

## Tech Stack

- **Backend**: Flask 3.1+, Flask-Login, Flask-SQLAlchemy
- **ML/AI**: scikit-learn, pandas, numpy
- **Frontend**: Bootstrap 5, Vanilla JavaScript
- **Database**: SQLite (default), PostgreSQL supported via GRIEVANCE_DB_URL

## Quick Start

### Local Development

1. **Clone and install dependencies**:
```bash
pip install -r requirements.txt
```

2. **Seed the database**:
```bash
python data/seed_data.py
```

3. **Train the ML model** (optional):
```bash
python app/ai/train_model.py
```

4. **Run the application**:
```bash
python run.py
```

5. **Access the app**: http://localhost:5000

### Replit Deployment

1. **Set Secrets** in Replit:
   - `SESSION_SECRET`: Random string for session security
   - `GRIEVANCE_DB_URL` (optional): Custom database URL (defaults to SQLite)

2. **Click Run** - The app will:
   - Install dependencies automatically
   - Create database tables
   - Start on port 5000

3. **Seed initial data**:
```bash
python data/seed_data.py
```

## Default Users

After running the seed script:

- **Admin**: username=`admin`, password=`admin123`
- **Officer**: username=`officer1`, password=`officer123`
- **Citizen**: username=`citizen1`, password=`citizen123`

⚠️ **Change these passwords in production!**

## Project Structure

```
.
├── app/
│   ├── __init__.py              # Flask app factory
│   ├── config.py                # Configuration
│   ├── models.py                # Database models
│   ├── ai/
│   │   ├── classifier.py        # ML classifier
│   │   ├── train_model.py       # Training script
│   │   └── data/
│   │       └── grievances.csv   # Training dataset
│   ├── blueprints/
│   │   ├── auth/                # Authentication
│   │   ├── grievances/          # Grievance management
│   │   ├── admin/               # Admin dashboard
│   │   └── api/                 # REST API
│   └── templates/               # Jinja2 templates
├── data/
│   └── seed_data.py             # Database seeding
├── tests/                       # Test suite
├── run.py                       # Entry point
└── LICENSE                      # MIT License
```

## ML Model Training

The system uses TF-IDF vectorization with Logistic Regression for department classification.

**To train the model**:
```bash
python app/ai/train_model.py
```

**Training data format** (CSV):
```csv
description,department
Water supply is not working,Water Department
Garbage collection delayed,Sanitation Department
```

**Fallback**: If no model exists, keyword-based heuristics classify grievances.

## REST API Documentation

### Authentication

**Register** (always creates a citizen user for security):
```bash
POST /api/v1/auth/register
Content-Type: application/json

{
  "username": "user1",
  "email": "user@example.com",
  "password": "password123",
  "full_name": "John Doe"
}
```

**Login**:
```bash
POST /api/v1/auth/login
Content-Type: application/json

{
  "username": "user1",
  "password": "password123"
}
```

### Grievances

**List Grievances**:
```bash
GET /api/v1/grievances?page=1&per_page=10
```

**Get Grievance**:
```bash
GET /api/v1/grievances/{id}
```

**Create Grievance**:
```bash
POST /api/v1/grievances
Content-Type: application/json

{
  "title": "Water supply issue",
  "description": "No water for 3 days",
  "location": "Main Street",
  "priority": "high"
}
```

**Update Status** (Officer/Admin only):
```bash
PATCH /api/v1/grievances/{id}/status
Content-Type: application/json

{
  "status_id": 3
}
```

### Reference Data

**Get Departments**:
```bash
GET /api/v1/departments
```

**Get Statuses**:
```bash
GET /api/v1/statuses
```

## Database Models

- **User**: Authentication and role management
- **Department**: Organizational departments
- **Status**: Grievance lifecycle states
- **Grievance**: Main grievance entity
- **Comment**: Discussion threads
- **Attachment**: File attachments (future)

## Architecture

### AI Classification

1. **ML-based** (if model exists):
   - TF-IDF vectorization of grievance description
   - Logistic Regression prediction
   - Returns department name

2. **Keyword-based** (fallback):
   - Searches for department keywords in description
   - Returns best match or "General Services"

### Access Control

- **Citizen**: Submit grievances, view own grievances, add comments
- **Officer**: View all grievances, update status, add comments
- **Admin**: Full access + dashboard with KPIs

## Testing

Run the test suite:
```bash
python -m pytest tests/
```

Or individually:
```bash
python tests/test_auth.py
python tests/test_grievances.py
python tests/test_api.py
```

## Production Deployment

For production deployment, ensure:

1. **Set Environment Variables**:
   - `FLASK_ENV=production` (disables debug mode)
   - `SESSION_SECRET=<strong-random-key>` (use cryptographically strong secret)
   - `GRIEVANCE_DB_URL=<database-url>` (optional, defaults to SQLite)

2. **Use Production WSGI Server** (not Flask development server):
   ```bash
   pip install gunicorn
   gunicorn -w 4 -b 0.0.0.0:5000 run:app
   ```

3. **Enable HTTPS** with reverse proxy (nginx/Apache)

4. **Change Default Passwords** for admin, officer, and citizen test accounts

## Security Considerations

- ✅ Password hashing with Werkzeug
- ✅ CSRF protection with Flask-WTF
- ✅ Role-based access control
- ✅ SQL injection prevention (SQLAlchemy ORM)
- ✅ API registration always creates 'citizen' role (prevents privilege escalation)
- ✅ Debug mode controlled by FLASK_ENV (defaults to production)
- ⚠️ Change default passwords in production
- ⚠️ Set strong SESSION_SECRET in production
- ⚠️ Use production WSGI server (gunicorn/uwsgi)
- ⚠️ Enable HTTPS in production
- ⚠️ Only admins should be able to change user roles (via admin panel)

## Future Enhancements

- [ ] File attachments for grievances
- [ ] Email/SMS notifications
- [ ] Advanced analytics and reporting
- [ ] Geolocation mapping
- [ ] Multi-language support
- [ ] Mobile app integration

## License

MIT License - See [LICENSE](LICENSE) file for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## Support

For issues or questions:
- Open an issue on GitHub
- Review the documentation
- Check test files for usage examples

---

**Built with ❤️ using Flask, scikit-learn, and Bootstrap**
