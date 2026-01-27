## **Staff OSINT – Who Reveals What (Conceptual Flow)**

**Employees**  
Employees reveal technical information through their social media profiles, such as listed skills, job roles, certifications, and shared content. From this, we can identify whether they work in development, security, DevOps, or management, and which technologies they actively use.

⬇️

**Job Posts**  
Job postings expose the company’s technology stack by listing required programming languages, frameworks, databases, tools, and development practices. This helps identify the backend, frontend, DevOps, and security technologies used in production.

⬇️

**Employee Profiles (LinkedIn/Xing)**  
Profiles provide deeper insight into real-world usage of technologies. Career history, project descriptions, and endorsements help estimate employee expertise and system complexity. Security-focused roles can indicate implemented defensive measures.

⬇️

**Public Code Repositories (GitHub)**  
GitHub repositories may reveal application structure, framework usage, and coding practices. In some cases, sensitive data such as personal emails, configuration files, or hardcoded secrets (e.g., JWT tokens) may be exposed due to poor security practices.

⬇️

**Framework Knowledge & Misconfigurations**  
Knowing the frameworks used (e.g., Django or Flask) allows attackers or pentesters to study common misconfigurations and predictable file structures. Developers often follow best practices, which can unintentionally make environments easier to analyze.

⬇️

**Final Outcome**  
By combining information from employees, job posts, and public repositories, we can map the company’s infrastructure, technology stack, and security maturity using only publicly available information. This makes staff-based OSINT a critical step in reconnaissance and penetration testing.
