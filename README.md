## Chat App

[Live demo on Vercel front end](https://vercel.com/brians-projects-04877b85/real-time-communication-with-socket-io-krispytank-2rcy/5QvQ2hscHgjv6Bz1sBHoDQ6giNrU)

# install (if not already)
npm install

# build optimized production assets
npm run build
# by default Vite outputs to `dist/`

# Environment variables
```

.env.development → VITE_API_URL=http://localhost:5000/api

.env.production → VITE_API_URL=https://real-time-communication-with-socket-tan.vercel.app/
```

# Production Error handling
```
const isProd = process.env.NODE_ENV === 'production';

module.exports = (err, req, res, next) => {
  console.error(err); // or structured log (winston)
  if (res.headersSent) return next(err);
  res.status(err.status || 500).json({
    message: isProd ? 'Internal server error' : err.message,
    // in prod do NOT include stack; in staging/dev include details
    ...(isProd ? {} : { stack: err.stack })
  });
};
```

# Secure Http Headers
```
npm install helmet cors
const helmet = require('helmet');
app.use(helmet());
app.use(cors({ origin: process.env.FRONTEND_URL || '*' }));

```

# Logging for production
```
npm install winston morgan

// logger.js
const { createLogger, transports, format } = require('winston');
module.exports = createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: format.combine(format.timestamp(), format.json()),
  transports: [new transports.Console()]
});

const morgan = require('morgan');
const logger = require('./logger');
app.use(morgan('combined', { stream: { write: (msg) => logger.info(msg.trim()) } }));

```

# Monitoring and Maintenace
```
// src/routes/health.js
const express = require('express');
const router = express.Router();
const mongoose = require('mongoose');

router.get('/health', async (req, res) => {
  const dbState = mongoose.connection.readyState; // 1 = connected
  res.json({ uptime: process.uptime(), dbState });
});

module.exports = router;

```