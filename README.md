# Discord Timestamp Generator

A modern, user-friendly tool for generating Discord-compatible timestamps with natural language support. Built with Vue.js and featuring a sleek dark mode interface.

## Features

- Natural language date/time input processing
- Support for all Discord timestamp formats
- Modern dark mode interface
- Real-time preview of timestamps
- One-click copying to clipboard
- Responsive design

## Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/discord-timestamp-gen.git
cd discord-timestamp-gen
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

## Usage

1. Enter a date/time in natural language (e.g., "next Friday at 3 PM", "tomorrow at noon", "in 2 hours")
2. Click "Generate" or press Enter
3. View the generated timestamps in various Discord formats
4. Click "Copy" next to any format to copy it to your clipboard
5. Paste the timestamp into Discord to see it in action!

## Supported Timestamp Formats

- Short Time (t) - 16:20
- Long Time (T) - 16:20:30
- Short Date (d) - 20/04/2023
- Long Date (D) - 20 April 2023
- Short Date/Time (f) - 20 April 2023 16:20
- Long Date/Time (F) - Thursday, 20 April 2023 16:20
- Relative Time (R) - 2 hours ago

## Technologies Used

- Vue.js 3
- Vite
- Tailwind CSS
- Compromise NLP
- date-fns
- Vue Toast Notification

## License

MIT License - feel free to use this project however you'd like!
 
