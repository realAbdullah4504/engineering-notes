# reCAPTCHA v3

## What it is

Google's invisible reCAPTCHA service that provides risk-based bot detection without user interaction. Returns a score (0.0-1.0) indicating likelihood of legitimate human traffic.

## Problem it solves

- **Bot protection**: Detects automated traffic without disrupting user experience
- **Risk assessment**: Provides granular risk scoring for different actions
- **Invisible protection**: No challenges or user interaction required
- **Adaptive security**: Adjusts protection based on traffic patterns

## When to use it

- **Form submissions**: Contact forms, registration, login attempts
- **API endpoints**: Sensitive operations like password changes
- **High-value actions**: Purchases, data exports, account deletions
- **Rate limiting supplement**: Additional layer beyond IP-based limits
- **Public-facing features**: Any anonymous user interaction

## When NOT to use it

- **Internal systems**: Trusted user environments
- **Admin interfaces**: Authenticated admin areas
- **API-only services**: When using other authentication methods
- **Simple static sites**: Low-risk content pages
- **Offline applications**: Systems without internet connectivity

## Integration pattern

### 1. Frontend Implementation
```javascript
// Load reCAPTCHA script
<script src="https://www.google.com/recaptcha/api.js?render=SITE_KEY"></script>

// Execute reCAPTCHA before form submission
grecaptcha.ready(function() {
  grecaptcha.execute('SITE_KEY', {action: 'submit'}).then(function(token) {
    // Add token to form or send via AJAX
    document.getElementById('recaptcha-token').value = token;
    // Submit form
  });
});
```

### 2. Backend Verification
```javascript
// Node.js/Express example
app.post('/submit', async (req, res) => {
  const { token, action } = req.body;
  
  const response = await fetch('https://www.google.com/recaptcha/api/siteverify', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: `secret=${SECRET_KEY}&response=${token}`
  });
  
  const result = await response.json();
  
  if (result.success && result.score >= 0.5) {
    // Process legitimate request
  } else {
    // Block suspicious request
  }
});
```

### 3. Score Thresholds
- **0.7+**: Low risk - allow critical actions
- **0.5-0.7**: Medium risk - allow with monitoring
- **0.3-0.5**: High risk - require additional verification
- **<0.3**: Very high risk - block or challenge

## Security considerations

### 1. Secret key protection
- Never expose secret keys in frontend code
- Store in environment variables or secure configuration
- Use different keys for development/production

### 2. Token validation
- Always verify tokens on the server
- Check token expiration (2 minutes)
- Verify action matches expected value
- Validate hostname matches your domain

### 3. Score interpretation
- Adjust thresholds based on your risk tolerance
- Consider different thresholds for different actions
- Monitor score distributions over time
- Implement gradual enforcement

### 4. Fallback strategy
- Handle service outages gracefully
- Implement rate limiting as backup protection
- Log failed verifications for analysis
- Consider alternative verification methods

## Example usage

### Contact Form Integration
```javascript
// Frontend
function submitContactForm(formData) {
  return grecaptcha.execute('SITE_KEY', {action: 'contact'})
    .then(token => {
      return fetch('/api/contact', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ ...formData, token, action: 'contact' })
      });
    });
}

// Backend
app.post('/api/contact', async (req, res) => {
  const { token, action, ...formData } = req.body;
  
  const recaptchaResult = await verifyRecaptcha(token, action);
  
  if (!recaptchaResult.success || recaptchaResult.score < 0.5) {
    return res.status(400).json({ error: 'Suspicious activity detected' });
  }
  
  // Process contact form submission
  await saveContactMessage(formData);
  res.json({ success: true });
});

async function verifyRecaptcha(token, action) {
  const response = await fetch('https://www.google.com/recaptcha/api/siteverify', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: `secret=${process.env.RECAPTCHA_SECRET}&response=${token}`
  });
  
  const result = await response.json();
  return result;
}
```

## Configuration

### Environment Variables
```
RECAPTCHA_SITE_KEY=your_site_key_here
RECAPTCHA_SECRET_KEY=your_secret_key_here
RECAPTCHA_MIN_SCORE=0.5
```

### Domain Configuration
- Add all domains (including subdomains) to reCAPTCHA admin console
- Include localhost for development
- Test staging environments before production deployment

## Monitoring and Maintenance

### Metrics to track
- Verification success/failure rates
- Score distribution by action type
- False positive rates
- Geographic patterns

### Regular tasks
- Review score thresholds quarterly
- Monitor for unusual traffic patterns
- Update domain list as needed
- Review Google's reCAPTCHA analytics dashboard

## Common pitfalls

- **Hardcoding keys**: Always use environment variables
- **Ignoring scores**: Don't just check success boolean
- **Missing action verification**: Always verify action matches
- **No fallback**: Handle service outages appropriately
- **Wrong thresholds**: Test and adjust for your use case
