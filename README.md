CommandLine
===========

C# command line class based from initial version of GSCoder's CommandLine class which only required 2 files.

Original version: https://github.com/gsscoder/commandline

See Example.cs for usage.

{code}
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;

namespace CommandLine
{
    public class Example
    {
        public static ExampleOptions Options = new ExampleOptions();

        public static int Main(string[] args)
        {
            try
            {
                Options = ExampleOptions.ParseArgs(args);
                if (Options == null)
                    return 1;

                Console.WriteLine("Successfully parsed the options");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Example error: {0}", ex);
                return 1;
            }
            return 0;
        }
    }

    public class ExampleOptions : CommandLineOptionsBase
    {
        [ValueList(typeof(List<string>))]
        public IList<string> Actions { get; set; }

        [Option("g", "game", Required = true, HelpText = "Name of game. ex: 'Test'")]
        public string Game { get; set; }

        [Option("b", "build", Required = true, HelpText = "Build version of game. ex: 'XXXX'")]
        public string Build { get; set; }

        [Option("m", "Manifest", Required = false, HelpText = "Path to manifest file")]
        public string Manifest { get; set; }

        [Option("v", "verbose", Required = false, HelpText = "Turns on verbose output")]
        public bool Verbose { get; set; }

        [HelpOption]
        public string GetUsage()
        {
            // this without using CommandLine.Text
            var usage = new StringBuilder();
            usage.AppendLine("");
            usage.AppendLine("CommandLine Example");
            usage.AppendLine("   Example.exe someAction --game Test --build Foo --Manifest bar");
            usage.AppendLine("");
            return usage.ToString();
        }

        public static ExampleOptions ParseArgs(string[] args)
        {
            var options = new ExampleOptions();
            var settings = new CommandLineParserSettings { CaseSensitive = false };
            var parser = new CommandLineParser(settings);
            var results = parser.ParseArguments(args, options);
            if (!results)
                return null;

            if (!string.IsNullOrEmpty(options.Manifest) && !File.Exists(options.Manifest))
            {
                Console.WriteLine("Missing manifest file: {0}", options.Manifest);
                return null;
            }

            return options;
        }
    }
}

{code}
