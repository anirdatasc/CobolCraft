# Mermaid Diagrams for COBOL to Java Migration Strategy

This directory contains all Mermaid diagrams extracted from and created for the COBOL to Java Migration Strategy document.

## Diagram Files

### Process Flowcharts

1. **01-high-level-migration-process.mmd** - Overview of the 6 migration phases
2. **02-detailed-migration-workflow.mmd** - Detailed step-by-step migration workflow
3. **03-code-migration-process.mmd** - Code migration process from COBOL to Java
4. **04-testing-strategy-flow.mmd** - Complete testing strategy and flow
5. **05-risk-management-process.mmd** - Risk identification and mitigation process

### Sequence Diagrams

6. **06-overall-migration-sequence.mmd** - Overall migration sequence with all stakeholders
7. **07-code-migration-sequence.mmd** - Code migration sequence diagram
8. **08-parallel-testing-sequence.mmd** - Parallel testing sequence with COBOL and Java systems
9. **09-deployment-sequence.mmd** - Deployment and cutover sequence

### Additional Diagrams

10. **10-migration-phases-timeline.mmd** - Gantt chart showing migration timeline
11. **11-team-roles-responsibilities.mmd** - Team structure and responsibilities
12. **12-data-migration-flow.mmd** - Data migration process flow
13. **13-architecture-overview.mmd** - Target Java architecture overview
14. **14-documentation-flow.mmd** - Documentation creation and maintenance flow
15. **15-migration-strategy-comparison.mmd** - Comparison of different migration strategies

## How to View

### Option 1: Online Viewers
- [Mermaid Live Editor](https://mermaid.live/) - Paste the diagram code to view
- [GitHub/GitLab](https://github.com) - View directly in markdown files
- [VS Code](https://code.visualstudio.com/) - Install Mermaid Preview extension

### Option 2: Command Line
```bash
# Install mermaid-cli
npm install -g @mermaid-js/mermaid-cli

# Generate PNG
mmdc -i 01-high-level-migration-process.mmd -o 01-high-level-migration-process.png

# Generate SVG
mmdc -i 01-high-level-migration-process.mmd -o 01-high-level-migration-process.svg
```

### Option 3: HTML Viewer
The diagrams are embedded in the `cobol_to_java_strategy.html` file and will render automatically when you load the markdown file.

## Diagram Types

- **Flowcharts**: Process flows and decision trees
- **Sequence Diagrams**: Interactions between components/people
- **Gantt Charts**: Timeline and scheduling
- **Graph Diagrams**: Relationships and architecture

## Usage

These diagrams can be:
- Embedded in documentation
- Used in presentations
- Included in project management tools
- Shared with stakeholders
- Modified for specific project needs

## Notes

- All diagrams use Mermaid syntax
- Diagrams are numbered for easy reference
- Color coding is used for visual clarity:
  - Green: Start/End points
  - Yellow: Decision points
  - Orange: Warnings/Important steps
  - Red: Risks/Issues
  - Blue/Purple: Different components

