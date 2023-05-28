name: Running Code Coverage

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}

    strategy:
      matrix:
        node-version: [12.x, 13.x, 14.x, 15.x]

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          fetch-depth: 2

      - name: Set up Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm run test

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      codecov:
        require_ci_to_pass: true
  
      coverage:
        precision: 2
        round: down
        range: '80...100'
  
      parsers:
        gcov:
        branch_detection:
        conditional: yes
        loop: yes
        method: no
        macro: no
  
      ignore:
        - '**/example/'
        - 'examples'
        - '**/*all.dart'
        - '**/*.pb.dart'
  
      comment:
        layout: 'reach,diff,flags,files,footer'
        behavior: default
        require_changes: false
        require_base: no # [yes :: must have a base report to post]
        require_head: yes # [yes :: must have a head report to post]
