- Definition: React is a declarative, efficient, and flexible JavaScript library used for building user interfaces through small, reusable pieces of code known as components.
- Key Features:
	- DOM Management: React manages the Document Object Model (DOM) automatically, allowing developers to define the desired UI state rather than manually updating the page.
	- Performance: Updates occur immediately within the browser, avoiding the need for server requests to fetch HTML blobs.
	- Local Storage: Data can be maintained and processed entirely within the browser until final submission, which is crucial for handling sensitive information like PII.
	- Scalability: Components are self-contained and easy to test, promoting a scalable approach to code organization.

	- Component Anatomy and Implementation
- Function Components: The standard way to write components is using functions, which take arguments (often called "props") and return a representation of how the UI should appear.
- State and Hooks: While props are arguments from parent components, internal state is managed using "hooks" (functions starting with "use," such as useState, useMemo, or useEffect).
- JSX: JSX is a syntax extension that resembles HTML and is used to define UI structures within JavaScript. It requires a build process to convert it into standard JavaScript.
- Comparison to Class Components: While older class-based components exist, function components with hooks are preferred for being more compact, less repetitive, and easier to manage regarding state and life-cycle side effects.

- Codebase Integration
- Rails Integration: The application uses Ruby on Rails view templates (ERB files) to render the root of the React application, passing data through HTML attributes to control configurations.
- Folder Structure:
	- Packages: These are treated as standalone NPM packages within a monorepo, containing generalized, reusable code.
	- Packs: These act as entry points and integration points between the Rails and React worlds, allowing the application to initialize with page-specific context.
- TypeScript: Although not fully implemented, the project uses TypeScript types (often via "Definitely Typed") to provide auto-completion and type checking to improve code maintainability.

- Testing
- React Testing Library: The team uses the React Testing Library to render components into an in-memory DOM (jsdom).
- Philosophy: This approach encourages accessible, semantic testing that focuses on user expectations rather than specific implementation details like CSS classes.
- Benefits: Testing in jsdom is faster than spinning up a full browser environment, though the team also utilizes feature tests that run through a real browser.