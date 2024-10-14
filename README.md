function convertToJson(inputString) {
  const lines = inputString.split('\n').map(line => line.trim()).filter(line => line);
  let result = {};
  let currentQuestion = '';
  let currentAnswer = [];

  for (let line of lines) {
    if (line.match(/^\d+\./) || line === 'Mobile app' || line.startsWith('For policies')) {
      if (currentQuestion) {
        result[currentQuestion] = currentAnswer.join('\n');
        currentAnswer = [];
      }
      currentQuestion = line;
    } else {
      currentAnswer.push(line);
    }
  }

  if (currentQuestion) {
    result[currentQuestion] = currentAnswer.join('\n');
  }

  return JSON.stringify(result, null, 2);
}

// Example usage:
const inputData = `1. Go to the Policies page <link to OA /policies> or the Home page <link to OA /dashboard>
2. Click View Policy
3. Then you can either:
   1. Click Edit under Payment Method to change account, OR
   2. Click Edit under Payment Plan to edit payment date or frequency.

Mobile app

1. Open your NRMA Insurance app. (Don't have the app? Download from the App Store or Google Play)
2. Click Manage Payments
3. Select your policy
4. Within Current Payment Method, click Edit and follow the prompts.

For policies in NSW, QLD, TAS and the ACT first purchased before 21 April 2024 and not yet renewed, or last renewed before 1 July 2024`;

console.log(convertToJson(inputData));
