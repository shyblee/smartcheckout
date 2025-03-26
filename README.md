# smartcheckout
# Smart Checkout System - Complete Setup Guide

## Project Structure
```
smart-checkout-system/
│
├── backend/                 # Raspberry Pi Python Backend
│   ├── src/
│   │   ├── main.py
│   │   ├── huskylens_detector.py
│   │   ├── loadcell_handler.py
│   │   ├── lcd_display.py
│   │   └── payment_qr_generator.py
│   │
│   ├── database/
│   │   └── products.json
│   ├── requirements.txt
│   └── setup.sh
│
├── frontend/                # React UI
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   └── SmartCheckoutUI.js
│   │   ├── services/
│   │   │   └── ProductService.js
│   │   ├── App.js
│   │   └── index.js
│   ├── package.json
│   └── README.md
│
├── api/                     # Backend API Bridge
│   ├── app.py               # Flask API 
│   └── requirements.txt
│
└── README.md
```

## Detailed Setup Process

### 1. Preliminary Setup (Raspberry Pi)

#### Step 1: Prepare Raspberry Pi
```bash
# Update system
sudo apt-get update
sudo apt-get upgrade -y

# Install essential packages
sudo apt-get install -y python3-pip python3-dev git

# Enable I2C and SPI
sudo raspi-config
# Navigate to Interfacing Options
# Enable I2C and SPI
```

#### Step 2: Backend Setup
```bash
# Navigate to backend directory
cd smart-checkout-system/backend

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Install specific libraries
pip install hx711 HUSKYLENS-PI RPi.GPIO smbus2
```

### 2. Frontend Setup (Development Machine)

#### Prerequisites
- Node.js (v14 or later)
- npm (Node Package Manager)

```bash
# Install Node.js and npm (on your development machine)
# Option 1: Using NodeSource
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs

# Option 2: Using NVM (Recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc
nvm install node
```

#### Frontend Installation
```bash
# Navigate to frontend directory
cd smart-checkout-system/frontend

# Install create-react-app globally (if not already installed)
npm install -g create-react-app

# Install project dependencies
npm install

# Install additional dependencies
npm install tailwindcss@latest postcss@latest autoprefixer@latest
npm install lucide-react axios
```

### 3. API Bridge Setup

#### Create Flask API Bridge
```bash
# Create API directory
cd smart-checkout-system/api

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install Flask and dependencies
pip install flask flask-cors
pip install -r requirements.txt
```

### 4. Configuration Files

#### Tailwind CSS Configuration (frontend)
```bash
# In frontend directory
npx tailwindcss init -p
```

##### tailwind.config.js
```javascript
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

##### src/index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 5. API Implementation (api/app.py)
```python
from flask import Flask, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

# Simulated backend services
class ProductService:
    @staticmethod
    def detect_item():
        items = ['Apple', 'Banana', 'Orange', 'Mango']
        return random.choice(items)
    
    @staticmethod
    def get_item_details(item_name):
        database = {
            'Apple': {'price': 0.50, 'weight_range': [150, 250]},
            'Banana': {'price': 0.30, 'weight_range': [100, 200]},
            # Add more items
        }
        return database.get(item_name, {})

@app.route('/detect-item')
def detect_item():
    item = ProductService.detect_item()
    details = ProductService.get_item_details(item)
    return jsonify({
        'item': item,
        'details': details
    })

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
```

### 6. Running the Project

#### Backend (Raspberry Pi)
```bash
# Activate virtual environment
cd smart-checkout-system/backend
source venv/bin/activate

# Run main script
python src/main.py
```

#### API Bridge
```bash
# Activate virtual environment
cd smart-checkout-system/api
source venv/bin/activate

# Run Flask API
python app.py
```

#### Frontend (Development Machine)
```bash
# Navigate to frontend directory
cd smart-checkout-system/frontend

# Start development server
npm start
```

### 7. Connecting Components

#### Frontend Service (frontend/src/services/ProductService.js)
```javascript
import axios from 'axios';

const API_URL = 'http://raspberrypi.local:5000';  // Replace with Raspberry Pi's IP

export const ProductService = {
  async detectItem() {
    try {
      const response = await axios.get(`${API_URL}/detect-item`);
      return response.data;
    } catch (error) {
      console.error('Error detecting item:', error);
      return null;
    }
  }
};
```

### 8. SSH Tunneling for Remote Access
```bash
# On Raspberry Pi
ssh -R 5000:localhost:5000 username@your-development-machine
```

### Deployment Considerations
1. Use `npm run build` for production frontend
2. Use Nginx or Apache for hosting
3. Set up systemd services for backend scripts

### Troubleshooting
- Ensure all dependencies are installed
- Check network configurations
- Verify hardware connections
- Monitor system logs

### Additional Resources
- [Raspberry Pi Documentation](https://www.raspberrypi.org/documentation/)
- [React Official Docs](https://reactjs.org/docs/getting-started.html)
- [Flask Documentation](https://flask.palletsprojects.com/)

### Estimated Setup Time
- Initial Setup: 2-3 hours
- Hardware Configuration: 1-2 hours
- Software Integration: 3-4 hours

### Cost Breakdown
- Raspberry Pi 3: $35-$45
- Sensors and Components: $100-$150
- Development Time: Varies

## Next Steps
1. Calibrate Load Cell
2. Train HuskyLens
3. Test individual components
4. Integrate and debug
