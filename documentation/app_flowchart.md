flowchart TD
    A[Landing Page] --> B[Sign Up]
    B --> C[Enter Email and Password]
    C --> D[Complete Profile Setup]
    D --> E[Send Verification Email]
    E --> F{Email Verified}
    F -->|Yes| G[Sign In]
    F -->|No| H[Resend Verification]
    G --> I{Authentication Result}
    I -->|Success| J[Dashboard]
    I -->|Failure| K[Invalid Credentials]
    K --> G
    J --> L[Training Modules]
    L --> M[Select Category]
    M --> N[Start Exercise]
    N --> O[Submit Answers]
    O --> P[Evaluate Performance]
    P --> Q[Adjust Difficulty]
    Q --> R[Points Awarded]
    R --> S{Next Action}
    S -->|Continue| N
    S -->|Return to Dashboard| J
    J --> T[Progress Page]
    J --> U[Settings Page]
    J --> V[Sign Out]