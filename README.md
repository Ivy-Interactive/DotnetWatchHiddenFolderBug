# dotnet watch Hidden Folder Bug Repro

Minimal reproduction for the issue where `dotnet watch` does not detect new files
when the project lives inside a hidden directory (a directory whose name starts with `.`).

## Steps to reproduce

1. Start the watcher:
   ```
   cd .hidden/DotnetWatchHiddenFolderBug
   dotnet watch --verbose
   ```

2. Verify the app is running by visiting `http://localhost:5000` (or whichever port is shown).

3. In another terminal, add a new file to the project:
   ```
   cat > NewEndpoint.cs << 'EOF'
   public static class NewEndpoint
   {
       public static void Map(WebApplication app)
       {
           app.MapGet("/new", () => "I am new!");
       }
   }
   EOF
   ```

4. **Expected:** `dotnet watch` detects the new file and triggers a rebuild.
   **Actual:** The new file is ignored — no rebuild occurs. The `/new` endpoint is not available.
   ```
   dotnet watch ⌚ Waiting for changes
   dotnet watch ⌚ [FW] Added '/Users/zachwolfe/Code/ivy-root/DotnetWatchHiddenFolderBug/.hidden/DotnetWatchHiddenFolderBug/NewEndpoint.cs'.
   dotnet watch ⌚ Ignoring change in hidden directory '/Users/zachwolfe/Code/ivy-root/DotnetWatchHiddenFolderBug/.hidden': Add '/Users/zachwolfe/Code/ivy-root/DotnetWatchHiddenFolderBug/.hidden/DotnetWatchHiddenFolderBug/NewEndpoint.cs'
   ```

5. For comparison, copy this same project to a non-hidden directory and repeat.
   `dotnet watch` will correctly detect the new file there.

## Environment

- .NET 10 SDK
- macOS
