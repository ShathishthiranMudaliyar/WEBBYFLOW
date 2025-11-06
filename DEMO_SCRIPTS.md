# ?? WEBBYFLOW - Demo Scripts for Interview

## ?? Preparation Checklist

Before your demo:
- [ ] System is running (Docker or local)
- [ ] Browser has demo websites open in tabs
- [ ] API keys are configured
- [ ] Test these scripts at least once
- [ ] Have backup recordings ready
- [ ] Internet connection is stable

---

## ?? Demo #1: Simple Google Search (30 seconds)

### **Purpose**: Show basic capability

### **Script**:

```python
import asyncio
from skyvern import Skyvern

async def simple_demo():
    """
    Demonstrates: Basic navigation and data extraction
    """
    print("?? DEMO #1: Simple Web Automation\n")
    print("Task: Search Google for 'AI automation' and extract top results\n")
    
    client = Skyvern()
    
    # Create task
    print("??  Creating task...")
    task = await client.create_task(
        url="https://www.google.com",
        navigation_goal="Search for 'AI automation tools'",
        data_extraction_goal="Extract the top 5 search result titles and URLs"
    )
    
    print(f"? Task created: {task.task_id}")
    print("? Executing... (watch the browser!)\n")
    
    # Wait for completion
    result = await client.get_task_result(task.task_id, timeout=60)
    
    # Display results
    print("? RESULTS:")
    print("?" * 60)
    for i, item in enumerate(result.extracted_information[:5], 1):
        print(f"{i}. {item['title']}")
        print(f"   {item['url']}\n")
    
    print(f"??  Completed in {result.execution_time:.2f}s")
    print(f"?? Steps taken: {len(result.steps)}")
    
    return result

# Run it
if __name__ == "__main__":
    asyncio.run(simple_demo())
```

### **Talking Points**:
1. "Notice we didn't write any XPath or CSS selectors"
2. "The AI understands what a search box looks like"
3. "It automatically clicks, types, and extracts data"
4. "This same code works on any search engine"

---

## ?? Demo #2: E-commerce Price Comparison (2 minutes)

### **Purpose**: Show cross-website capability

### **Script**:

```python
import asyncio
from skyvern import Skyvern

async def ecommerce_demo():
    """
    Demonstrates: Multi-site automation with structured extraction
    """
    print("?? DEMO #2: E-commerce Price Comparison\n")
    print("Task: Find iPhone 15 Pro prices across 3 websites\n")
    
    client = Skyvern()
    
    websites = [
        ("Amazon", "https://www.amazon.com"),
        ("Best Buy", "https://www.bestbuy.com"),
        ("Target", "https://www.target.com")
    ]
    
    results = {}
    
    for name, url in websites:
        print(f"??  Searching {name}...")
        
        task = await client.create_task(
            url=url,
            navigation_goal="Search for 'iPhone 15 Pro 256GB'",
            data_extraction_goal="""
                Extract:
                - Product name
                - Price (numeric value)
                - Availability status
                - Star rating
            """
        )
        
        result = await client.get_task_result(task.task_id, timeout=90)
        results[name] = result.extracted_information
        
        print(f"? {name}: ${result.extracted_information['price']}")
        print(f"   Rating: {result.extracted_information.get('rating', 'N/A')}\n")
    
    # Find best price
    print("\n?? COMPARISON RESULTS:")
    print("?" * 60)
    
    sorted_results = sorted(
        results.items(), 
        key=lambda x: float(x[1]['price'].replace('$', '').replace(',', ''))
    )
    
    for i, (site, data) in enumerate(sorted_results, 1):
        emoji = "??" if i == 1 else "??" if i == 2 else "??"
        print(f"{emoji} {site}: ${data['price']}")
        print(f"   {data['availability']}")
    
    print(f"\n?? Best deal: {sorted_results[0][0]} - ${sorted_results[0][1]['price']}")
    
    return results

# Run it
if __name__ == "__main__":
    asyncio.run(ecommerce_demo())
```

### **Talking Points**:
1. "Same code works on three different websites"
2. "No site-specific customization needed"
3. "AI adapts to each site's layout automatically"
4. "Returns structured, comparable data"

---

## ?? Demo #3: Form Filling Intelligence (3 minutes)

### **Purpose**: Show reasoning capability

### **Script**:

```python
import asyncio
from skyvern import Skyvern

async def intelligent_form_demo():
    """
    Demonstrates: Context-aware form filling with reasoning
    """
    print("?? DEMO #3: Intelligent Form Filling\n")
    print("Task: Fill out insurance quote form with smart reasoning\n")
    
    client = Skyvern()
    
    # User profile
    profile = {
        "name": "John Smith",
        "birth_date": "1990-05-15",
        "license_date": "2008-03-20",  # Got license at 18
        "address": "123 Main St, Los Angeles, CA 90001",
        "vehicle": "2020 Honda Civic",
        "current_insurance": "Yes",
        "accidents": 0
    }
    
    print("?? User Profile:")
    for key, value in profile.items():
        print(f"   {key}: {value}")
    
    print("\n??  Navigating to insurance form...")
    
    task = await client.create_task(
        url="https://www.geico.com/quote",
        navigation_goal="Fill out auto insurance quote form completely",
        navigation_payload=profile,
        data_extraction_goal="Extract the monthly premium quote"
    )
    
    print("? Filling form... (AI is making intelligent decisions)\n")
    
    result = await client.get_task_result(task.task_id, timeout=180)
    
    # Show AI reasoning
    print("?? AI REASONING EXAMPLES:")
    print("?" * 60)
    
    for step in result.steps:
        if step.reasoning:
            print(f"Question: {step.page_context}")
            print(f"AI Thought: {step.reasoning}")
            print(f"Action: {step.action_taken}\n")
    
    print("\n? FINAL QUOTE:")
    print("?" * 60)
    print(f"Monthly Premium: ${result.extracted_information['monthly_premium']}")
    print(f"Annual Premium: ${result.extracted_information['annual_premium']}")
    print(f"Coverage Level: {result.extracted_information['coverage']}")
    
    print(f"\n??  Completed in {result.execution_time:.2f}s")
    print(f"?? Fields filled: {result.metadata['fields_completed']}")
    
    return result

# Run it
if __name__ == "__main__":
    asyncio.run(intelligent_form_demo())
```

### **Example AI Reasoning**:

```
Question: "Were you eligible to drive at 18?"
Context: User got license at 2008-03-20 (18 years old)
AI Reasoning: "User received license at 18, so they were 
               eligible to drive at 18. Answer: Yes"
Action: Click "Yes" radio button

Question: "Do you have any accidents in the last 5 years?"
Context: profile['accidents'] = 0
AI Reasoning: "User profile shows 0 accidents. Answer: No"
Action: Click "No" radio button

Question: "What is your vehicle year?"
Context: profile['vehicle'] = "2020 Honda Civic"
AI Reasoning: "Extract year from vehicle string: 2020"
Action: Type "2020" in year field
```

### **Talking Points**:
1. "AI doesn't just fill forms blindly"
2. "It reasons about questions and available data"
3. "Handles complex conditional logic"
4. "Can infer information not explicitly provided"

---

## ?? Demo #4: Workflow Automation (5 minutes)

### **Purpose**: Show complex multi-step workflows

### **Script**:

```python
import asyncio
from skyvern import Skyvern

async def workflow_demo():
    """
    Demonstrates: Complex workflows with conditional logic
    """
    print("?? DEMO #4: Complex Workflow Automation\n")
    print("Scenario: Research and report on job market\n")
    
    client = Skyvern()
    
    # Define workflow
    workflow = await client.create_workflow(
        name="Job Market Research",
        blocks=[
            {
                "type": "task",
                "label": "Search Indeed",
                "url": "https://www.indeed.com",
                "navigation_goal": "Search for 'Python Developer' jobs in San Francisco",
                "data_extraction_goal": "Extract top 10 job listings with salary ranges"
            },
            {
                "type": "task",
                "label": "Search LinkedIn",
                "url": "https://www.linkedin.com/jobs",
                "navigation_goal": "Search for 'Python Developer' jobs in San Francisco",
                "data_extraction_goal": "Extract top 10 job listings with company info"
            },
            {
                "type": "task",
                "label": "Search Glassdoor",
                "url": "https://www.glassdoor.com",
                "navigation_goal": "Search for Python Developer salary data in San Francisco",
                "data_extraction_goal": "Extract average salary and salary range"
            },
            {
                "type": "validation",
                "condition": "Compare and aggregate data from all sources"
            }
        ]
    )
    
    print("?? Workflow Steps:")
    for i, block in enumerate(workflow.blocks, 1):
        print(f"   {i}. {block['label']}")
    
    print("\n??  Executing workflow...\n")
    
    # Execute
    run = await client.execute_workflow(workflow.workflow_id)
    
    # Monitor progress
    while run.status in ["running", "queued"]:
        print(f"? Status: {run.status} | Progress: {run.progress}%")
        await asyncio.sleep(5)
        run = await client.get_workflow_run(run.run_id)
    
    # Aggregate results
    print("\n? WORKFLOW COMPLETED!\n")
    print("?? AGGREGATED RESULTS:")
    print("?" * 60)
    
    all_jobs = []
    for block_result in run.block_results:
        if block_result.block_type == "task":
            jobs = block_result.extracted_information
            all_jobs.extend(jobs)
            print(f"{block_result.label}: {len(jobs)} jobs found")
    
    print(f"\nTotal jobs found: {len(all_jobs)}")
    
    # Calculate statistics
    salaries = [j['salary'] for j in all_jobs if j.get('salary')]
    avg_salary = sum(salaries) / len(salaries) if salaries else 0
    
    print(f"Average salary: ${avg_salary:,.2f}")
    print(f"Salary range: ${min(salaries):,.2f} - ${max(salaries):,.2f}")
    
    # Top companies
    companies = {}
    for job in all_jobs:
        company = job.get('company')
        if company:
            companies[company] = companies.get(company, 0) + 1
    
    print("\n?? Top Hiring Companies:")
    for company, count in sorted(companies.items(), key=lambda x: x[1], reverse=True)[:5]:
        print(f"   {company}: {count} openings")
    
    # Generate report
    print("\n?? Generating PDF report...")
    report_path = await client.generate_report(run.run_id, format="pdf")
    print(f"? Report saved: {report_path}")
    
    return run

# Run it
if __name__ == "__main__":
    asyncio.run(workflow_demo())
```

### **Talking Points**:
1. "Chains multiple tasks together"
2. "Aggregates data from multiple sources"
3. "Conditional logic and validation"
4. "Generates final reports automatically"

---

## ?? Demo #5: Error Handling & Recovery (2 minutes)

### **Purpose**: Show robustness and self-healing

### **Script**:

```python
import asyncio
from skyvern import Skyvern

async def error_recovery_demo():
    """
    Demonstrates: Self-healing automation and error recovery
    """
    print("???  DEMO #5: Error Handling & Recovery\n")
    print("Scenario: Handling challenging websites\n")
    
    client = Skyvern()
    
    # Deliberately challenging scenarios
    scenarios = [
        {
            "name": "Slow Loading Page",
            "url": "https://example-slow-site.com",
            "expected_issue": "Timeout waiting for elements"
        },
        {
            "name": "Dynamic Content",
            "url": "https://example-dynamic-site.com",
            "expected_issue": "Elements change after load"
        },
        {
            "name": "CAPTCHA Challenge",
            "url": "https://example-captcha-site.com",
            "expected_issue": "CAPTCHA detection"
        }
    ]
    
    for scenario in scenarios:
        print(f"\n??  Testing: {scenario['name']}")
        print(f"   Expected challenge: {scenario['expected_issue']}")
        
        task = await client.create_task(
            url=scenario['url'],
            navigation_goal="Complete the form",
            max_retries=3,
            timeout=120
        )
        
        # Monitor with detailed logging
        print("   Executing with error recovery...")
        
        result = await client.get_task_result(
            task.task_id, 
            verbose=True
        )
        
        if result.status == "completed":
            print(f"   ? Success after {result.retry_count} retries")
            print(f"   Recovery strategies used:")
            for strategy in result.recovery_strategies:
                print(f"      - {strategy}")
        else:
            print(f"   ??  Failed: {result.error_message}")
            print(f"   Attempted {result.retry_count} times")
    
    print("\n?? ERROR RECOVERY CAPABILITIES:")
    print("?" * 60)
    print("? Automatic retry on failure")
    print("? Alternative element selection")
    print("? Wait time adjustment")
    print("? Page refresh and retry")
    print("? CAPTCHA solving integration")
    print("? Graceful degradation")

# Run it
if __name__ == "__main__":
    asyncio.run(error_recovery_demo())
```

### **Talking Points**:
1. "Doesn't fail on first error"
2. "Tries multiple strategies automatically"
3. "Learns from failures"
4. "Graceful degradation when needed"

---

## ?? LIVE DEMO Best Practices

### **Do's**:
? Test everything before the interview
? Have recordings as backup
? Explain what's happening in real-time
? Show the browser window (visual is powerful)
? Highlight AI decision-making
? Compare to traditional approach
? Mention business value/ROI
? Keep it under 5 minutes per demo

### **Don'ts**:
? Don't depend on live internet
? Don't use sites that might block you
? Don't show failures (unless demonstrating recovery)
? Don't go too technical initially
? Don't skip explaining the "why"

---

## ?? Demo Flow Suggestion

**Total Time: 10-12 minutes**

1. **Opening** (30 sec)
   - "Let me show you WEBBYFLOW in action"
   - Pull up browser and terminal

2. **Demo #1: Simple Search** (1 min)
   - Show basic capability
   - Highlight no coding needed

3. **Demo #2 or #3** (2-3 min)
   - Choose based on audience:
     - Business people ? E-commerce comparison
     - Technical people ? Intelligent form filling

4. **Show Architecture** (2 min)
   - Pull up `SYSTEM_ARCHITECTURE.md`
   - Explain multi-agent system
   - Show technology stack

5. **Show Results** (2 min)
   - Dashboard with completed tasks
   - Screenshots showing step-by-step
   - Extracted data

6. **Q&A and Deep Dive** (5+ min)
   - Be ready for technical questions
   - Can show code if needed
   - Discuss scalability

---

## ?? Recording Backup Plan

If live demo fails, have these recorded:

```bash
# Record your demos beforehand
# Screen recording: OBS Studio (free) or Windows Game Bar

# Save to:
C:\Users\shath\Source\Repos\skyvern\demos\
??? demo1_simple_search.mp4
??? demo2_ecommerce.mp4
??? demo3_intelligent_forms.mp4
??? demo4_workflow.mp4
??? demo5_error_recovery.mp4
```

---

## ?? Closing Statement After Demo

"What you just saw demonstrates three key advantages:

1. **Speed**: Tasks that take hours to code take minutes to set up
2. **Reliability**: AI adapts to changes automatically - no maintenance
3. **Scalability**: Same approach works across thousands of websites

This is the future of web automation - intelligent, adaptive, and production-ready."

---

## ?? Pro Tips

1. **Practice your demo 5+ times before interview**
2. **Have terminal and browser side-by-side**
3. **Use `print()` statements to narrate what's happening**
4. **Prepare for "What if" questions**:
   - "What if the site changes?" ? Show self-healing
   - "What if it fails?" ? Show error recovery
   - "How fast is it?" ? Show metrics
   - "Can it scale?" ? Show architecture

5. **Connect everything to business value**:
   - "This saves 20 hours per week"
   - "This reduces maintenance costs by 90%"
   - "This allows non-technical users to automate"

---

**Good luck! You've got this! ??**
