# strong-password-checker
class Solution(object):
    def strongPasswordChecker(self, password):
        """
        :type password: str
        :rtype: int
        """
        n = len(password)
        
        # Check missing character types
        missing_lower = 1
        missing_upper = 1
        missing_digit = 1
        
        for c in password:
            if c.islower(): missing_lower = 0
            elif c.isupper(): missing_upper = 0
            elif c.isdigit(): missing_digit = 0
        
        missing_types = missing_lower + missing_upper + missing_digit
        
        # Step 1: Count repeating sequences
        repeats = []
        i = 0
        while i < n:
            j = i
            while j < n and password[j] == password[i]:
                j += 1
            length = j - i
            if length >= 3:
                repeats.append(length)
            i = j
        
        # If short password (<6)
        if n < 6:
            return max(missing_types, 6 - n)
        
        # If length is OK (6–20)
        if n <= 20:
            replace = 0
            for r in repeats:
                replace += r // 3
            return max(missing_types, replace)
        
        # If too long (>20)
        delete_needed = n - 20
        replace = 0
        
        # Use deletions smartly to reduce replacements
        buckets = [0, 0, 0]
        for r in repeats:
            buckets[r % 3] += 1
        
        # First delete where r % 3 == 0
        to_delete = delete_needed
        for k in range(3):
            idx = k
            while buckets[idx] > 0 and to_delete > idx:
                to_delete -= (idx + 1)
                buckets[idx] -= 1
        
        # Remaining deletions can't directly reduce all replacements
        replace = 0
        for r in repeats:
            if r >= 3:
                r -= min(r - 2, delete_needed)
                replace += r // 3
        
        return delete_needed + max(missing_types, replace)
