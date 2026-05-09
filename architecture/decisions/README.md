# Architecture Decisions

This directory contains architectural decision records (ADRs) that capture important engineering decisions and their rationale.

## Structure

Each decision follows the format: `00X-decision-name.md`

- Numbers indicate the timeline of decisions
- Names clearly describe the decision scope
- Each file contains one complete architectural decision

## Recent Decisions

- [001: Choose ECS Fargate for Celery Deployment](001-celery-ecs-deployment.md) - 2026-05-09

## Decision Categories

- **Deployment**: How we deploy and manage services
- **Data**: Database and storage choices
- **Processing**: Queue systems and background jobs
- **Architecture**: System structure and patterns
- **Infrastructure**: Runtime and operational choices

## Evolution

Decisions evolve as systems grow. Each decision includes:
- Clear justification based on current constraints
- Specific evolution triggers
- Migration paths when applicable

## Related Documentation

- [Decision Map Documentation Rule](../../.windsurf/rules/decision-map-documentation-rule.md)
- [Architecture Documentation](../README.md)
- [System Evolution](../system-evolution/)
