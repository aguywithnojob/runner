name: Auto Merger

on:
  workflow_run:
    workflows: ["Label Checker"]
    types:
      - completed

jobs:
  auto-merge:
    # if: github.event.label.name == 'Merge Ready'
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest

    # steps:  
    #   - name: Checkout code
    #     uses: actions/checkout@v4

    #   - name: Merge PR
    #     env:
    #       GH_TOKEN: ${{ secrets.GH_PAT }}
    #     run: |
    #       PR_NUMBER=${{ github.event.pull_request.number }}
    #       echo "Merging PR #$PR_NUMBER..."
          
    #       # Optional: check PR status (e.g., if it's open, etc.)
    #       gh pr view $PR_NUMBER --json state -q ".state" | {
    #         read STATE
    #         echo "Current PR state::: $STATE"
    #         if [[ "$STATE" == "OPEN" ]]; then
    #           gh pr merge $PR_NUMBER --merge --delete-branch --admin
    #         else
    #           echo "PR not ready: state=$STATE"
    #           exit 1
    #         fi
    #       }
    steps:
      - name: Checkout code
        uses: actions/checkout@v4  

      - name: Get PR number
        id: pr
        uses: actions/github-script@v6
        with:
          github-token: ${{ secrets.GH_PAT }}
          script: |
            const run = await github.rest.actions.getWorkflowRun({
              owner: context.repo.owner,
              repo: context.repo.repo,
              run_id: context.payload.workflow_run.id
            });
            const pr = run.data.pull_requests[0];
            core.setOutput("number", pr.number);

      - name: Merge PR
        env:
          GH_TOKEN: ${{ secrets.GH_PAT }}
        run: |
          PR_NUMBER=${{ steps.pr.outputs.number }}
          echo "Merging PR #$PR_NUMBER..."

          # Optional: check PR status (e.g., if it's open, etc.)
          gh pr view $PR_NUMBER --json state -q ".state" | {
            read STATE
            echo "Current PR state::: $STATE"
            if [[ "$STATE" == "OPEN" ]]; then
              gh pr merge $PR_NUMBER --merge --delete-branch --admin
            else
              echo "PR not ready: state=$STATE"
              exit 1
            fi
          }
