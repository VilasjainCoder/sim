# sim
simprg
import random
from datetime import datetime, timedelta

# Simple SIM card class
class SIMCard:
    def __init__(self, phone_number):
        self.phone_number = phone_number
        self.sim_id = "SIM-" + str(random.randint(10000, 99999))
        self.active = False
        self.data_plan = "Basic"
        self.data_limit = 2  # GB
        self.data_used = 0
        self.balance = 0
        self.expiry_date = None
    
    def activate(self):
        """Activate the SIM card"""
        self.active = True
        self.expiry_date = datetime.now() + timedelta(days=30)
        return f"SIM card {self.sim_id} activated until {self.expiry_date.strftime('%Y-%m-%d')}"
    
    def deactivate(self):
        """Deactivate the SIM card"""
        self.active = False
        return f"SIM card {self.sim_id} deactivated"
    
    def add_balance(self, amount):
        """Add money to SIM balance"""
        self.balance += amount
        return f"Added ${amount}. New balance: ${self.balance}"
    
    def use_data(self, amount):
        """Use data from the plan"""
        if not self.active:
            return "SIM card is not active"
        
        if self.data_used + amount > self.data_limit:
            return "Data limit exceeded"
        
        self.data_used += amount
        return f"Used {amount}GB. Remaining: {self.data_limit - self.data_used}GB"
    
    def change_plan(self, plan_name):
        """Change the data plan"""
        plans = {
            "Basic": 2,
            "Standard": 5,
            "Premium": 10,
            "Unlimited": float('inf')
        }
        
        if plan_name not in plans:
            return "Invalid plan"
        
        self.data_plan = plan_name
        self.data_limit = plans[plan_name]
        return f"Changed to {plan_name} plan with {self.data_limit}GB data"
    
    def get_info(self):
        """Get SIM card information"""
        status = "Active" if self.active else "Inactive"
        expiry = self.expiry_date.strftime('%Y-%m-%d') if self.expiry_date else "Not activated"
        
        return f"""
SIM ID: {self.sim_id}
Phone Number: {self.phone_number}
Status: {status}
Plan: {self.data_plan}
Data: {self.data_used}GB used / {self.data_limit}GB total
Balance: ${self.balance}
Expiry: {expiry}
"""


# Customer class
class Customer:
    def __init__(self, name, email):
        self.name = name
        self.email = email
        self.customer_id = "CUST-" + str(random.randint(1000, 9999))
        self.sim_cards = []
    
    def add_sim(self, sim_card):
        """Add a SIM card to this customer"""
        self.sim_cards.append(sim_card)
        return f"Added SIM card {sim_card.sim_id} to {self.name}'s account"
    
    def get_info(self):
        """Get customer information"""
        return f"""
Customer ID: {self.customer_id}
Name: {self.name}
Email: {self.email}
Number of SIM cards: {len(self.sim_cards)}
"""


# SIM Provider class
class SIMProvider:
    def __init__(self, company_name):
        self.company_name = company_name
        self.customers = []
        self.sim_cards = []
    
    def create_phone_number(self):
        """Generate a random phone number"""
        area = random.randint(100, 999)
        middle = random.randint(100, 999)
        end = random.randint(1000, 9999)
        return f"{area}-{middle}-{end}"
    
    def create_customer(self, name, email):
        """Create a new customer"""
        customer = Customer(name, email)
        self.customers.append(customer)
        return customer
    
    def create_sim(self, customer):
        """Create a new SIM card for a customer"""
        phone_number = self.create_phone_number()
        sim_card = SIMCard(phone_number)
        self.sim_cards.append(sim_card)
        customer.add_sim(sim_card)
        return sim_card
    
    def find_customer(self, customer_id):
        """Find a customer by ID"""
        for customer in self.customers:
            if customer.customer_id == customer_id:
                return customer
        return None
    
    def find_sim(self, sim_id):
        """Find a SIM card by ID"""
        for sim in self.sim_cards:
            if sim.sim_id == sim_id:
                return sim
        return None
    
    def generate_bill(self, customer):
        """Generate bill for a customer"""
        bill_id = "BILL-" + str(random.randint(10000, 99999))
        total = 0
        
        bill = f"""
BILL ID: {bill_id}
Date: {datetime.now().strftime('%Y-%m-%d')}
Customer: {customer.name} ({customer.customer_id})

SIM CARDS:
"""
        for sim in customer.sim_cards:
            if sim.active:
                plan_prices = {
                    "Basic": 20,
                    "Standard": 30,
                    "Premium": 50,
                    "Unlimited": 80
                }
                price = plan_prices[sim.data_plan]
                total += price
                
                bill += f"- {sim.phone_number} ({sim.data_plan} Plan): ${price}\n"
                bill += f"  Data used: {sim.data_used}GB / {sim.data_limit}GB\n"
        
        bill += f"\nTotal Amount: ${total}"
        return bill


# Example usage
def run_simple_demo():
    # Create a new SIM provider company
    provider = SIMProvider("EasyTel")
    
    # Create a customer
    customer = provider.create_customer("Jane Smith", "jane@example.com")
    print("Created new customer:")
    print(customer.get_info())
    
    # Create SIM cards for the customer
    sim1 = provider.create_sim(customer)
    sim2 = provider.create_sim(customer)
    
    # Activate SIMs and set up plans
    print(sim1.activate())
    print(sim1.change_plan("Premium"))
    print(sim1.add_balance(100))
    print(sim1.use_data(3.5))
    
    print(sim2.activate())
    print(sim2.change_plan("Basic"))
    
    # Print SIM information
    print("\nSIM Card Details:")
    print(sim1.get_info())
    print(sim2.get_info())
    
    # Generate a bill
    print("\nMonthly Bill:")
    print(provider.generate_bill(customer))


if __name__ == "__main__":
    run_simple_demo()
